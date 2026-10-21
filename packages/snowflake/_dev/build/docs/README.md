{{- generatedHeader }}
# Snowflake Integration for Elastic

## Overview

The Snowflake integration for Elastic collects security telemetry from [Snowflake](https://www.snowflake.com/) `ACCOUNT_USAGE` views and event tables usage using the [SQL API](https://docs.snowflake.com/en/developer-guide/sql-api/intro). It covers object and column access, cross-cloud and cross-region data transfer, and stage lifecycle changes so you can monitor data access and possible exfiltration paths in Elasticsearch.

### Compatibility

This integration queries Snowflake `ACCOUNT_USAGE` historical views and the account event table through SQL API v2 (`/api/v2/statements`). Those views retain about one year of data and are delayed (about 45 minutes to 3 hours depending on the view). It is compatible with Snowflake accounts on AWS, Azure, and Google Cloud, including PrivateLink hosts. China region hosts (`.snowflakecomputing.cn`) are supported when the account URL is set accordingly. The `access_history` data stream requires Snowflake Enterprise Edition or later.

### How it works

Elastic Agent submits SQL statements to `POST /api/v2/statements` and pages result partitions. If Snowflake returns HTTP 202, the agent polls the statement handle until results are ready. Each data stream queries one `ACCOUNT_USAGE` view and advances a timestamp watermark with lookback to cover view latency. When a statement returns a full `batch_size` page, the next request continues from the last returned `ORDER BY` key without the lag overlap until the backlog is drained.

## What data does this integration collect?

The Snowflake integration collects the following logs:

* **access_history**: Objects and columns read or written by each query from `SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY` (direct and base objects accessed, objects modified, DDL operations, policies referenced). Enterprise Edition or later.
* **data_transfer_history**: Hourly byte-count windows of data moved between clouds and regions from `SNOWFLAKE.ACCOUNT_USAGE.DATA_TRANSFER_HISTORY`, by transfer type.
* **stages**: Stage create, alter, and drop activity from `SNOWFLAKE.ACCOUNT_USAGE.STAGES`, including external stage locations (`s3://`, `azure://`, `gcs://`) and storage integrations. Watermarks are `LAST_ALTERED` and `DELETED` because rows update in place.
* **query_history**: Completed statements from `SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY` (SQL text, user, role, warehouse, status, bytes, and timing).
* **events**: Handler **log messages** (`RECORD_TYPE` `LOG`) from the account event table (default `SNOWFLAKE.TELEMETRY.EVENTS`). Rows with `SPAN`, `SPAN_EVENT`, `METRIC`, or `EVENT` are dropped during ingest and are not stored in Elasticsearch.
* **external_access_history**: Outbound network calls from UDF and procedure handlers in `SNOWFLAKE.ACCOUNT_USAGE.EXTERNAL_ACCESS_HISTORY`, joined to `QUERY_HISTORY` for event time.

### Supported use cases

Use this integration to audit which users touch sensitive tables and columns, detect unusual outbound data transfer volumes, and track the creation or alteration of external stages that could be used to move data out of Snowflake.

## What do I need to use this integration?

* An Elastic Stack deployment (self-managed or Elastic Cloud) that meets the package Kibana version condition.
* A Snowflake account and a virtual warehouse that can auto-resume.
* A Snowflake `TYPE=SERVICE` user with a role that can query the views and use the warehouse.
* A programmatic access token for the service user. Username/password, OAuth access tokens, and key-pair JWT are not supported by this integration.

## How do I deploy this integration?

### Agent-based deployment

Elastic Agent must be installed. For more details, check the Elastic Agent [installation instructions](https://www.elastic.co/guide/en/fleet/current/elastic-agent-installation.html). You can install only one Elastic Agent per host.

Elastic Agent is required to stream data from the SQL API and ship the data to Elastic, where the events will then be processed via the integration's ingest pipelines.

### Set up steps in Snowflake

1. Create a role and a `TYPE=SERVICE` user, and grant the role to the user.
2. Grant `USAGE` on a warehouse that can auto-resume.
3. Grant the SNOWFLAKE database roles the data streams need (or `IMPORTED PRIVILEGES ON DATABASE SNOWFLAKE`):
   * `SNOWFLAKE.GOVERNANCE_VIEWER` for `access_history`
   * `SNOWFLAKE.USAGE_VIEWER` for `data_transfer_history`
   * `SNOWFLAKE.OBJECT_VIEWER` for `stages`
   * `SNOWFLAKE.GOVERNANCE_VIEWER` for `query_history`
   * `SNOWFLAKE.USAGE_VIEWER` for `access history`
   * `SNOWFLAKE.EVENTS_VIEWER` (or `SELECT` on a custom event table) for the `events`
4. Create a programmatic access token for the service user.
5. If a network policy is enabled, allow the Elastic Agent egress IP (or PrivateLink endpoint).

#### Vendor resources

- [SQL API introduction](https://docs.snowflake.com/en/developer-guide/sql-api/intro)
- [Authenticating to the SQL API](https://docs.snowflake.com/en/developer-guide/sql-api/authenticating)
- [Programmatic access tokens](https://docs.snowflake.com/en/user-guide/programmatic-access-tokens)
- [Account Usage](https://docs.snowflake.com/en/sql-reference/account-usage)
- [SNOWFLAKE database roles](https://docs.snowflake.com/en/sql-reference/snowflake-db-roles)
- [Event table setup](https://docs.snowflake.com/en/developer-guide/logging-tracing/event-table-setting-up)

### Set up steps in Kibana

1. In Kibana go to **Management → Integrations**.
2. Search for **Snowflake** and add the integration.
3. Set the account URL, role, and warehouse.
4. Provide the programmatic access token for the service user. OAuth access tokens and key-pair JWT are not supported.
5. Enable the data streams you want and save the policy.

### Validation

In Discover, filter on `event.dataset: snowflake.access_history`, `event.dataset: snowflake.data_transfer_history`, `event.dataset: snowflake.stages`, `event.dataset: snowflake.query_history`, `event.dataset: snowflake.events`, or `event.dataset: snowflake.external_access_history`. 
Access history should contain `event.category: database` events. Data transfer should contain `event.category: network` events with `network.bytes`. Stages should contain `event.category: configuration` events with `event.action` of `create`, `alter`, or `drop`. Query history should contain `event.category: database` events. External access should contain `event.category: network` events.

## Troubleshooting

- No data is being collected: Confirm the warehouse can auto-resume, the role has the required SNOWFLAKE database role (or imported privileges), and the account URL is reachable on HTTPS 443. ACCOUNT_USAGE latency means new rows can take up to 2–3 hours to appear.
- `access_history` returns an error: The `ACCESS_HISTORY` view exists only on Enterprise Edition or later. Turn off the data stream on lower editions.
- Authentication failures: Service users cannot use passwords. Provide a programmatic access token. The integration always sends `X-Snowflake-Authorization-Token-Type: PROGRAMMATIC_ACCESS_TOKEN`.
- Network policy blocks the SQL API: Add the agent egress IP, or use a PrivateLink hostname from `SYSTEM$GET_PRIVATELINK_CONFIG()`.
- HTTP 429 (`390505`): Reduce poll frequency. There is no documented `Retry-After` contract on the SQL API.
- Duplicate rows: Each poll overlaps the previous watermark by the configured lag time so late-arriving rows are included. Documents use a fingerprint `_id`, so re-delivered rows are usually dropped as duplicates, never overwritten, and a few duplicates can appear across index rollovers.
- QUERY_TEXT can contain secrets: Collecting `query_history` stores statement text, which may include literals or tokens.
- External access rows have no native timestamp: The stream joins `QUERY_HISTORY` on `QUERY_ID` and watermarks on `END_TIME`.

## Performance and scaling

Each poll runs SQL on the configured warehouse and consumes Snowflake credits. `ACCOUNT_USAGE` latency is 45 minutes to 3 hours, so polling faster than the view latency does not make data fresher. Suggested starting intervals are `3h` for access history and external access history, `2h` for data transfer history, stages, and events, and `1h` for query history. Overlap the watermark by at least the view latency so late-arriving rows are included. `ACCESS_HISTORY` can be high volume on busy accounts. Keep `batch_size` at the default unless statements time out.

For more information on architectures that can be used for scaling this integration, check the [Ingest Architectures](https://www.elastic.co/docs/manage-data/ingest/ingest-reference-architectures) documentation.

## Reference

### Inputs used

These inputs can be used with this integration:
<details>
<summary>cel</summary>

## Setup

For more details about the CEL input settings, check the [Filebeat documentation](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-cel.html).

Before configuring the CEL input, make sure you have:
- Network connectivity to the target API endpoint
- Valid authentication credentials (API keys, tokens, or certificates as required)
- Appropriate permissions to read from the target data source

### Collecting logs from CEL

To configure the CEL input, you must specify the `request.url` value pointing to the API endpoint. The interval parameter controls how frequently requests are made and is the primary way to balance data freshness with API rate limits and costs. Authentication is often configured through the `request.headers` section using the appropriate method for the service.

NOTE: To access the API service, make sure you have the necessary API credentials and that the Filebeat instance can reach the endpoint URL. Some services may require IP whitelisting or VPN access.

To collect logs via API endpoint, configure the following parameters:

- API Endpoint URL
- API credentials (tokens, keys, or username/password)
- Request interval (how often to fetch data)
</details>


### API usage

These APIs are used with this integration:

* [Submit SQL](https://docs.snowflake.com/en/developer-guide/sql-api/submitting-requests) — `POST /api/v2/statements`
* [Poll status and fetch partitions](https://docs.snowflake.com/en/developer-guide/sql-api/handling-responses) — `GET /api/v2/statements/{statementHandle}`

### Vendor documentation links

- [SQL API reference](https://docs.snowflake.com/en/developer-guide/sql-api/reference)
- [ACCESS_HISTORY view](https://docs.snowflake.com/en/sql-reference/account-usage/access_history)
- [DATA_TRANSFER_HISTORY view](https://docs.snowflake.com/en/sql-reference/account-usage/data_transfer_history)
- [STAGES view](https://docs.snowflake.com/en/sql-reference/account-usage/stages)
- [QUERY_HISTORY view](https://docs.snowflake.com/en/sql-reference/account-usage/query_history)
- [EXTERNAL_ACCESS_HISTORY view](https://docs.snowflake.com/en/sql-reference/account-usage/external_access_history)
- [Event table columns](https://docs.snowflake.com/en/developer-guide/logging-tracing/event-table-columns)

### Data streams

#### access_history

The `access_history` data stream provides object and column access events from `SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY`. Nested object lists are decoded from the view's JSON columns and kept as flattened fields. Object names and column names (which vary per query) are also extracted into keyword arrays for aggregation.

##### access_history fields

{{ fields "access_history" }}

##### access_history sample event

{{ event "access_history" }}

#### data_transfer_history

The `data_transfer_history` data stream provides hourly data transfer windows from `SNOWFLAKE.ACCOUNT_USAGE.DATA_TRANSFER_HISTORY`. Bytes transferred are mapped to `network.bytes` and the transfer type to `event.action`.

##### data_transfer_history fields

{{ fields "data_transfer_history" }}

##### data_transfer_history sample event

{{ event "data_transfer_history" }}

#### stages

The `stages` data stream provides stage lifecycle events from `SNOWFLAKE.ACCOUNT_USAGE.STAGES`. Rows update in place, so the collector watermarks on `LAST_ALTERED` and `DELETED`, and the pipeline derives `create`, `alter`, or `drop` from the timestamps.

##### stages fields

{{ fields "stages" }}

##### stages sample event

{{ event "stages" }}

#### query_history

The `query_history` data stream provides completed SQL statement events from
`SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY`. The collector watermarks on `END_TIME`.

##### query_history fields

{{ fields "query_history" }}

##### query_history sample event

{{ event "query_history" }}

#### events

The `events` data stream collects handler **log messages** from the account event table (default `SNOWFLAKE.TELEMETRY.EVENTS`). Only rows with `RECORD_TYPE` `LOG` are ingested; `SPAN`, `SPAN_EVENT`, `METRIC`, and `EVENT` rows are dropped by the ingest pipeline. Confirm the table name with `SHOW PARAMETERS LIKE 'EVENT_TABLE' IN ACCOUNT`.

##### events fields

{{ fields "events" }}

##### events sample event

{{ event "events" }}

#### external_access_history

The `external_access_history` data stream provides outbound network access events from `SNOWFLAKE.ACCOUNT_USAGE.EXTERNAL_ACCESS_HISTORY`, joined to `QUERY_HISTORY` for `END_TIME`.

##### external_access_history fields

{{ fields "external_access_history" }}

##### external_access_history sample event

{{ event "external_access_history" }}
