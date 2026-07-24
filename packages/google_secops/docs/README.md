# Google SecOps

[Google SecOps](https://cloud.google.com/chronicle/docs/secops/secops-overview) is a cloud-based service designed for enterprises to retain, analyze, and search large volumes of security and network telemetry. It normalizes, indexes, and correlates data to detect threats. Investigate their scope and cause, and provide remediation through pre-built integrations. The platform enables security analysts to examine aggregated security information, search across domains, and mitigate threats throughout their lifecycle.

The Google SecOps integration collects alerts using the [Detection Engine API](https://cloud.google.com/chronicle/docs/reference/detection-engine-api#listdetections).

## Compatibility

This module has been tested against the Google SecOps version **v2**.

## Data streams

This integration collects the following logs:

- **[Alerts v2](https://cloud.google.com/chronicle/docs/reference/rest/v1alpha/projects.locations.instances.legacy/legacySearchDetections)** (`google_secops.alert_v2`) - Retrieves YARA-L rule detections using the Chronicle API `legacySearchDetections` method. **Use this data stream for new deployments.**
- **[Alerts](https://cloud.google.com/chronicle/docs/reference/detection-engine-api#response_fields_3)** (`google_secops.alert`) - **Deprecated.** Retrieves alerts using the Backstory Detection Engine API. Only existing Google SecOps Backstory users can access this API. New users should use **Alerts v2** instead.

## Requirements

### Agentless deployment

Agentless deployments are only supported in Elastic Serverless and Elastic Cloud environments. Agentless deployments provide a means to ingest data while avoiding the orchestration, management, and maintenance needs associated with standard ingest infrastructure. Using an agentless deployment makes manual agent deployment unnecessary, allowing you to focus on your data instead of the agent that collects it.

For more information, refer to [Agentless integrations](https://www.elastic.co/guide/en/serverless/current/security-agentless-integrations.html) and [Agentless integrations FAQ](https://www.elastic.co/guide/en/serverless/current/agentless-integration-troubleshooting.html)

### Agent-based deployment

Elastic Agent must be installed. For more details, check the Elastic Agent [installation instructions](docs-content://reference/fleet/install-elastic-agents.md). You can install only one Elastic Agent per host.

## Setup

### Collect data from the Google SecOps API

1. Create Google SecOps service account [Steps to create](https://developers.google.com/identity/protocols/oauth2/service-account#creatinganaccount).

**Chronicle API** must be enabled.

### Enable the Chronicle API

1. Log in to the  "https://console.cloud.google.com/"  using valid credentials.
2. Navigate to the **Chronicle API**.
3. Click **Enable**.

### Update the Permission of Service Account

1. Open GCP Console, and go to IAM.
2. In **View By Main Tab**, click **GRANT ACCESS**.
3. Add Service Account name in **New Principals**.
4. In **Assign Role**, select **Owner**.
5. Click **Save**.

This integration will make use of the following *oauth2 scope*:

- `https://www.googleapis.com/auth/chronicle-backstory`

Once Service Account credentials are downloaded as a JSON file, then the integration can be setup to collect data.
For more details, please refer [Google Chronicle Detection Engine API]( https://cloud.google.com/chronicle/docs/reference/detection-engine-api#getting_api_authentication_credentials).

If installing in GCP-Cloud environment, credentials are not necessary but make sure the account linked with the VM has all the required IAM permissions. Steps to [Set up Application Default Credentials](https://cloud.google.com/docs/authentication/provide-credentials-adc).

### Collect data for the Alerts v2 data stream

Enable the **Alerts v2** data stream to collect from the Chronicle API [`legacySearchDetections`](https://cloud.google.com/chronicle/docs/reference/rest/v1alpha/projects.locations.instances.legacy/legacySearchDetections) method. Documents ingest into the `google_secops.alert_v2` data stream.

1. Assign **Chronicle Viewer** (`roles/chronicle.viewer`) to the service account in IAM.
2. Configure OAuth scope `https://www.googleapis.com/auth/chronicle.readonly` (or another supported Chronicle scope). See [Chronicle API authentication](https://cloud.google.com/chronicle/docs/reference/authentication).
3. Note your regional Chronicle API URL, GCP project ID, location, and instance ID from Google SecOps. See [Chronicle API endpoints](https://cloud.google.com/chronicle/docs/reference/rest).

### Enabling the integration in Elastic:

1. In the top search bar in Kibana, search for **Integrations**.
2. In "Search for integrations" top bar, search for `Google SecOps`.
3. Select the "Google SecOps" integration from the search results.
4. Select "Add Google SecOps" to add the integration.
5. Add all the required integration configuration parameters to enable data collection:
   - For **Alerts v2**, enable the **Alerts v2** data stream and add the regional Chronicle API URL (for example, `https://us-chronicle.googleapis.com`), GCP project ID, location, instance ID, OAuth scope, Credentials Type, and Credentials.
   - For **Alerts** (deprecated; existing Backstory users only), add the URL, Credentials Type, and Credentials.
6. Select "Save and continue" to save the integration.

**Note**: The default URL is `https://backstory.googleapis.com`, but this may vary depending on your region. Please refer to the [Documentation](https://cloud.google.com/chronicle/docs/reference/search-api#regional_endpoints) to find the correct URL for your region.

## Logs reference

### Alert

> **Deprecated:** The `alert` data stream uses the Backstory Detection Engine API and is only available to existing Google SecOps Backstory users. New users should use the [`alert_v2`](#alert-v2) data stream instead.

This is the `alert` dataset.

#### Example

An example event for `alert` looks as following:

```json
{
    "@timestamp": "2025-02-01T03:23:28.000Z",
    "agent": {
        "ephemeral_id": "781dc4c4-3906-4614-9ef3-6c0e38d8f65c",
        "id": "9e093d9c-46e3-4787-af0f-b75a22101e27",
        "name": "elastic-agent-83937",
        "type": "filebeat",
        "version": "8.19.18"
    },
    "data_stream": {
        "dataset": "google_secops.alert",
        "namespace": "80492",
        "type": "logs"
    },
    "destination": {
        "user": {
            "group": {
                "id": [
                    "0"
                ]
            },
            "id": "0",
            "name": "root"
        }
    },
    "ecs": {
        "version": "8.17.0"
    },
    "elastic_agent": {
        "id": "9e093d9c-46e3-4787-af0f-b75a22101e27",
        "snapshot": true,
        "version": "8.19.18"
    },
    "event": {
        "agent_id_status": "verified",
        "created": "2025-02-01T03:12:54.177Z",
        "dataset": "google_secops.alert",
        "end": "2025-02-03T03:23:28.000Z",
        "id": "de_66bf2e94-f97e-2564-1a75-2fdbf8cb6403",
        "ingested": "2026-06-30T12:42:34Z",
        "kind": "alert",
        "original": "{\"createdTime\":\"2025-02-01T03:12:54.177084Z\",\"detection\":{\"alertState\":\"NOT_ALERTING\",\"description\":\"This rule is to generate alerts when the event_type is STATUS_UPDATE\",\"outcomes\":[{\"key\":\"risk_score\",\"value\":\"60\"}],\"riskScore\":60,\"ruleId\":\"ru_123873a9a-170d-1234-a63d-9874f33ee011\",\"ruleLabels\":[{\"key\":\"author\",\"value\":\"John\"},{\"key\":\"description\",\"value\":\"This rule is to generate alerts when the event_type is STATUS_UPDATE\"},{\"key\":\"severity\",\"value\":\"Medium\"}],\"ruleName\":\"rule_to_detect_status_update\",\"ruleType\":\"SINGLE_EVENT\",\"ruleVersion\":\"ru_123873a9a-170d-1234-a63d-9874f33ee011@v_1732873302_954607000\",\"urlBackToProduct\":\"https://example.com\",\"variables\":{\"risk_score\":{\"int64Val\":\"60\",\"type\":\"OUTCOME\",\"value\":\"60\"}}},\"detectionTime\":\"2025-02-01T03:23:28Z\",\"event\":{\"about\":[{\"labels\":[{\"key\":\"header_time_milliseconds_offset\",\"value\":\"612\"}]}],\"additional\":{\"arguments_fd\":\"8\",\"event_modifier\":\"0\",\"exec_chain_thread_uuid\":\"5AB2623F-F6EF-4A6C-B2E4-CC7E28BEB515\",\"header_time_milliseconds_offset\":\"612\",\"header_version\":\"11\",\"identity_cd_hash\":\"a70ddfe3eb75dd35005a9c863c4174d63148406c\",\"identity_signer_id\":\"com.apple.curl\",\"identity_signer_id_truncated\":\"false\",\"identity_signer_type\":\"1\",\"identity_team_id_truncated\":\"false\",\"key\":\"6CC2ABE4-385C-4444-8BC0-FD5B618BA1C1\",\"subject_audit_id\":\"4294967295\",\"subject_terminal_id_type\":\"4-IPv4\"},\"metadata\":{\"baseLabels\":{\"allowScopedAccess\":true,\"logTypes\":[\"JAMF_TELEMETRY\"]},\"enrichmentLabels\":{\"allowScopedAccess\":true},\"eventTimestamp\":\"2025-02-03T03:23:28Z\",\"eventType\":\"STATUS_UPDATE\",\"id\":\"AAAAAByuGF66kDlZ79NglQZk0cQPPPPPBgSSSSSSSSS=\",\"ingestedTimestamp\":\"2025-02-01T06:00:42.443096Z\",\"logType\":\"JAMF_TELEMETRY\",\"productEventType\":\"AUE_CONNECT-32\",\"productName\":\"JAMF_TELEMETRY\",\"vendorName\":\"JAMF\"},\"network\":{\"sessionId\":\"100001\"},\"principal\":{\"asset\":{\"hardware\":[{\"serialNumber\":\"PPX94A9874\"}],\"hostname\":\"TEST-PPX94A9874\",\"productObjectId\":\"45DE0BEE-8056-5B41-B09A-08E259E49317\",\"software\":[{\"version\":\"Version 15.2 (Build 24C101)\"}]},\"group\":{\"groupDisplayName\":\"wheel\"},\"hostname\":\"TEST-PPX94A9874\",\"ip\":[\"0.0.0.0\"],\"labels\":[{\"key\":\"arguments_fd\",\"value\":\"8\"}],\"process\":{\"file\":{\"fullPath\":\"/bin/bash\",\"md5\":\"b14dba7fe27186f216037a3b60599582\",\"sha1\":\"47bba82e8a43cfa14a1124a477090f9fbd0e026a\",\"sha256\":\"4d8b9a54a2077c1457410843a9842ef29e0f371fb4061097095758012c031809\"},\"pid\":\"47203\"},\"processAncestors\":[{\"file\":{\"fullPath\":\"/usr/bin/curl\"},\"pid\":\"47325\"}],\"user\":{\"groupIdentifiers\":[\"0\"],\"userDisplayName\":\"root\",\"userid\":\"0\"}},\"securityResult\":[{\"description\":\"0-success\",\"detectionFields\":[{\"key\":\"return_value\",\"value\":\"0\"}]}],\"target\":{\"group\":{\"groupDisplayName\":\"wheel\"},\"user\":{\"groupIdentifiers\":[\"0\"],\"userDisplayName\":\"root\",\"userid\":\"0\"}}},\"id\":\"de_66bf2e94-f97e-2564-1a75-2fdbf8cb6403\",\"label\":\"e\",\"timeWindow\":{\"endTime\":\"2025-02-03T03:23:28Z\",\"startTime\":\"2025-02-01T03:23:28Z\"},\"type\":\"RULE_DETECTION\"}",
        "risk_score": 60,
        "start": "2025-02-01T03:23:28.000Z"
    },
    "google_secops": {
        "alert": {
            "createdTime": "2025-02-01T03:12:54.177Z",
            "detection": {
                "alertState": "NOT_ALERTING",
                "description": "This rule is to generate alerts when the event_type is STATUS_UPDATE",
                "outcomes": [
                    {
                        "key": "risk_score",
                        "value": "60"
                    }
                ],
                "riskScore": 60,
                "ruleId": "ru_123873a9a-170d-1234-a63d-9874f33ee011",
                "ruleLabels": [
                    {
                        "key": "author",
                        "value": "John"
                    },
                    {
                        "key": "description",
                        "value": "This rule is to generate alerts when the event_type is STATUS_UPDATE"
                    },
                    {
                        "key": "severity",
                        "value": "Medium"
                    }
                ],
                "ruleName": "rule_to_detect_status_update",
                "ruleType": "SINGLE_EVENT",
                "ruleVersion": "ru_123873a9a-170d-1234-a63d-9874f33ee011@v_1732873302_954607000",
                "urlBackToProduct": "https://example.com",
                "variables": {
                    "risk_score": {
                        "int64Val": 60,
                        "type": "OUTCOME",
                        "value": 60
                    }
                }
            },
            "detectionTime": "2025-02-01T03:23:28.000Z",
            "event": {
                "about": [
                    {
                        "labels": [
                            {
                                "key": "header_time_milliseconds_offset",
                                "value": "612"
                            }
                        ]
                    }
                ],
                "additional": {
                    "arguments_fd": "8",
                    "event_modifier": "0",
                    "exec_chain_thread_uuid": "5AB2623F-F6EF-4A6C-B2E4-CC7E28BEB515",
                    "header_time_milliseconds_offset": "612",
                    "header_version": "11",
                    "identity_cd_hash": "a70ddfe3eb75dd35005a9c863c4174d63148406c",
                    "identity_signer_id": "com.apple.curl",
                    "identity_signer_id_truncated": "false",
                    "identity_signer_type": "1",
                    "identity_team_id_truncated": "false",
                    "key": "6CC2ABE4-385C-4444-8BC0-FD5B618BA1C1",
                    "subject_audit_id": "4294967295",
                    "subject_terminal_id_type": "4-IPv4"
                },
                "metadata": {
                    "baseLabels": {
                        "allowScopedAccess": true,
                        "logTypes": [
                            "JAMF_TELEMETRY"
                        ]
                    },
                    "enrichmentLabels": {
                        "allowScopedAccess": true
                    },
                    "eventTimestamp": "2025-02-03T03:23:28.000Z",
                    "eventType": "STATUS_UPDATE",
                    "id": "AAAAAByuGF66kDlZ79NglQZk0cQPPPPPBgSSSSSSSSS=",
                    "ingestedTimestamp": "2025-02-01T06:00:42.443Z",
                    "logType": "JAMF_TELEMETRY",
                    "productEventType": "AUE_CONNECT-32",
                    "productName": "JAMF_TELEMETRY",
                    "vendorName": "JAMF"
                },
                "network": {
                    "sessionId": "100001"
                },
                "principal": {
                    "asset": {
                        "hardware": [
                            {
                                "serialNumber": "PPX94A9874"
                            }
                        ],
                        "hostname": "TEST-PPX94A9874",
                        "productObjectId": "45DE0BEE-8056-5B41-B09A-08E259E49317",
                        "software": [
                            {
                                "version": "Version 15.2 (Build 24C101)"
                            }
                        ]
                    },
                    "group": {
                        "groupDisplayName": "wheel"
                    },
                    "hostname": "TEST-PPX94A9874",
                    "ip": [
                        "0.0.0.0"
                    ],
                    "labels": [
                        {
                            "key": "arguments_fd",
                            "value": "8"
                        }
                    ],
                    "process": {
                        "file": {
                            "fullPath": "/bin/bash",
                            "md5": "b14dba7fe27186f216037a3b60599582",
                            "sha1": "47bba82e8a43cfa14a1124a477090f9fbd0e026a",
                            "sha256": "4d8b9a54a2077c1457410843a9842ef29e0f371fb4061097095758012c031809"
                        },
                        "pid": 47203
                    },
                    "processAncestors": [
                        {
                            "file": {
                                "fullPath": "/usr/bin/curl"
                            },
                            "pid": "47325"
                        }
                    ],
                    "user": {
                        "groupIdentifiers": [
                            "0"
                        ],
                        "userDisplayName": "root",
                        "userid": "0"
                    }
                },
                "securityResult": [
                    {
                        "description": "0-success",
                        "detectionFields": [
                            {
                                "key": "return_value",
                                "value": "0"
                            }
                        ]
                    }
                ],
                "target": {
                    "group": {
                        "groupDisplayName": "wheel"
                    },
                    "user": {
                        "groupIdentifiers": [
                            "0"
                        ],
                        "userDisplayName": "root",
                        "userid": "0"
                    }
                }
            },
            "friendly_name": "rule_to_detect_status_update",
            "id": "de_66bf2e94-f97e-2564-1a75-2fdbf8cb6403",
            "label": "e",
            "timeWindow": {
                "endTime": "2025-02-03T03:23:28.000Z",
                "startTime": "2025-02-01T03:23:28.000Z"
            },
            "type": "RULE_DETECTION"
        }
    },
    "host": {
        "hostname": "TEST-PPX94A9874",
        "ip": [
            "0.0.0.0"
        ]
    },
    "input": {
        "type": "cel"
    },
    "observer": {
        "product": "JAMF_TELEMETRY",
        "vendor": "JAMF"
    },
    "process": {
        "executable": "/bin/bash",
        "hash": {
            "md5": "b14dba7fe27186f216037a3b60599582",
            "sha1": "47bba82e8a43cfa14a1124a477090f9fbd0e026a",
            "sha256": "4d8b9a54a2077c1457410843a9842ef29e0f371fb4061097095758012c031809"
        },
        "pid": 47203
    },
    "related": {
        "hash": [
            "b14dba7fe27186f216037a3b60599582",
            "47bba82e8a43cfa14a1124a477090f9fbd0e026a",
            "4d8b9a54a2077c1457410843a9842ef29e0f371fb4061097095758012c031809"
        ],
        "hosts": [
            "TEST-PPX94A9874"
        ],
        "ip": [
            "0.0.0.0"
        ],
        "user": [
            "root",
            "0"
        ]
    },
    "rule": {
        "description": "This rule is to generate alerts when the event_type is STATUS_UPDATE",
        "id": "ru_123873a9a-170d-1234-a63d-9874f33ee011",
        "name": "rule_to_detect_status_update",
        "version": "ru_123873a9a-170d-1234-a63d-9874f33ee011@v_1732873302_954607000"
    },
    "tags": [
        "preserve_original_event",
        "preserve_duplicate_custom_fields",
        "forwarded",
        "google_secops-alert"
    ],
    "user": {
        "group": {
            "id": [
                "0"
            ],
            "name": "wheel"
        },
        "id": "0",
        "name": "root"
    }
}
```

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| event.dataset | Event dataset. | constant_keyword |
| event.module | Event module. | constant_keyword |
| google_secops.alert.createdTime | Time the detection was created. | date |
| google_secops.alert.detection.alertState | Indicates whether the rule generating this detection currently has alerting enabled or disabled. | keyword |
| google_secops.alert.detection.description | Description of the rule that generated the detection. This description is copied from the rule metadata's `description` key, if it is set. | keyword |
| google_secops.alert.detection.detectionFields.key | Key for a field specified in the rule, for "MULTI_EVENT" rules. | keyword |
| google_secops.alert.detection.detectionFields.source |  | keyword |
| google_secops.alert.detection.detectionFields.value | Value for a field specified in the rule, for "MULTI_EVENT" rules. | keyword |
| google_secops.alert.detection.outcomes.key |  | keyword |
| google_secops.alert.detection.outcomes.value |  | keyword |
| google_secops.alert.detection.riskScore |  | long |
| google_secops.alert.detection.risk_score.int64Val |  | long |
| google_secops.alert.detection.risk_score.type |  | keyword |
| google_secops.alert.detection.risk_score.value |  | long |
| google_secops.alert.detection.ruleId | Identifier for the rule generating the detection. | keyword |
| google_secops.alert.detection.ruleLabels.key | Key for a field specified in the rule metadata. | keyword |
| google_secops.alert.detection.ruleLabels.value | Value for a field specified in the rule metadata. | keyword |
| google_secops.alert.detection.ruleName | Name of the rule generating the detection, as parsed from `ruleText`. | keyword |
| google_secops.alert.detection.ruleType | Whether the rule generating this detection is a single event or multi-event rule ("SINGLE_EVENT" or "MULTI_EVENT"). | keyword |
| google_secops.alert.detection.ruleVersion | Identifier for the rule version generating the detection. | keyword |
| google_secops.alert.detection.urlBackToProduct | URL pointing to the Google Security Operations application page for this detection. | keyword |
| google_secops.alert.detection.variables.risk_score.int64Val |  | long |
| google_secops.alert.detection.variables.risk_score.type |  | keyword |
| google_secops.alert.detection.variables.risk_score.value |  | long |
| google_secops.alert.detectionTime | String representing the time period the detection was found in. | date |
| google_secops.alert.event.about.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.about.group.groupDisplayName | Group display name. e.g. "Finance". | keyword |
| google_secops.alert.event.about.labels.key | The key. | keyword |
| google_secops.alert.event.about.labels.value | The value. | keyword |
| google_secops.alert.event.about.url | The URL. | keyword |
| google_secops.alert.event.about.user.groupIdentifiers | Product object identifiers of the group(s) the user belongs to A vendor-specific identifier to uniquely identify the group(s) the user belongs to (a GUID, LDAP OID, or similar). | keyword |
| google_secops.alert.event.about.user.userDisplayName | The display name of the user (e.g. "John Locke"). | keyword |
| google_secops.alert.event.about.user.userid | The ID of the user. | keyword |
| google_secops.alert.event.additional |  | flattened |
| google_secops.alert.event.extracted |  | flattened |
| google_secops.alert.event.intermediary.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert.event.metadata.baseLabels.allowScopedAccess |  | boolean |
| google_secops.alert.event.metadata.baseLabels.ingestionKvLabels.key |  | keyword |
| google_secops.alert.event.metadata.baseLabels.ingestionKvLabels.value |  | keyword |
| google_secops.alert.event.metadata.baseLabels.logTypes |  | keyword |
| google_secops.alert.event.metadata.description | A human-readable unparsable description of the event. | keyword |
| google_secops.alert.event.metadata.enrichmentLabels.allowScopedAccess |  | boolean |
| google_secops.alert.event.metadata.enrichmentLabels.ingestionKvLabels.key |  | keyword |
| google_secops.alert.event.metadata.enrichmentLabels.ingestionKvLabels.value |  | keyword |
| google_secops.alert.event.metadata.enrichmentLabels.logTypes |  | keyword |
| google_secops.alert.event.metadata.eventTimestamp | The GMT timestamp when the event was generated. | date |
| google_secops.alert.event.metadata.eventType | The event type. If an event has multiple possible types, this specifies the most specific type. | keyword |
| google_secops.alert.event.metadata.id | ID of the UDM event. Can be used for raw and normalized event retrieval. | keyword |
| google_secops.alert.event.metadata.ingestedTimestamp | The GMT timestamp when the event was ingested (received) by Google Security Operations. | date |
| google_secops.alert.event.metadata.ingestionLabels.key |  | keyword |
| google_secops.alert.event.metadata.ingestionLabels.value |  | keyword |
| google_secops.alert.event.metadata.logType | The string value of log type. | keyword |
| google_secops.alert.event.metadata.productDeploymentId | The deployment identifier assigned by the vendor for a product deployment. | keyword |
| google_secops.alert.event.metadata.productEventType | A short, descriptive, human-readable, product-specific event name or type (for example: "Scanned X", "User account created", "process_start"). | keyword |
| google_secops.alert.event.metadata.productLogId | A vendor-specific event identifier to uniquely identify the event (for example: a GUID). | keyword |
| google_secops.alert.event.metadata.productName | The name of the product. | keyword |
| google_secops.alert.event.metadata.urlBackToProduct | A URL that takes the user to the source product console for this event. | keyword |
| google_secops.alert.event.metadata.vendorName | The name of the product vendor. | keyword |
| google_secops.alert.event.network.applicationProtocol | The application protocol. | keyword |
| google_secops.alert.event.network.dns.answers.data | The payload or response to the DNS question for all responses encoded in UTF-8 format. | keyword |
| google_secops.alert.event.network.dns.answers.name | The name of the owner of the resource record. | keyword |
| google_secops.alert.event.network.dns.answers.type | The code specifying the type of the resource record. | keyword |
| google_secops.alert.event.network.dns.questions.name | The domain name. | keyword |
| google_secops.alert.event.network.dns.questions.type | The code specifying the type of the query. | keyword |
| google_secops.alert.event.network.dnsDomain | DNS domain name. | keyword |
| google_secops.alert.event.network.email.bcc | A list of 'bcc' addresses. | keyword |
| google_secops.alert.event.network.email.cc | A list of 'cc' addresses. | keyword |
| google_secops.alert.event.network.email.from | The 'from' address. | keyword |
| google_secops.alert.event.network.email.replyTo | The 'reply to' address. | keyword |
| google_secops.alert.event.network.email.subject | The subject line(s) of the email. | keyword |
| google_secops.alert.event.network.email.to | A list of 'to' addresses. | keyword |
| google_secops.alert.event.network.ftp.command | The FTP command. | keyword |
| google_secops.alert.event.network.http.method | The HTTP request method (e.g. "GET", "POST", "PATCH", "DELETE"). | keyword |
| google_secops.alert.event.network.http.referralUrl | The URL for the HTTP referer. | keyword |
| google_secops.alert.event.network.http.responseCode | The response status code, for example 200, 302, 404, or 500. | long |
| google_secops.alert.event.network.http.userAgent | The User-Agent request header which includes the application type, operating system, software vendor or software version of the requesting software user agent. | keyword |
| google_secops.alert.event.network.ipProtocol | The IP protocol. | keyword |
| google_secops.alert.event.network.sessionId | The ID of the network session. | keyword |
| google_secops.alert.event.principal.asset.assetId | The asset ID. | keyword |
| google_secops.alert.event.principal.asset.attribute.labels.key | The key. | keyword |
| google_secops.alert.event.principal.asset.attribute.labels.value | The value. | keyword |
| google_secops.alert.event.principal.asset.hardware.serialNumber | Hardware serial number. | keyword |
| google_secops.alert.event.principal.asset.hostname | Asset hostname or domain name field. | keyword |
| google_secops.alert.event.principal.asset.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert.event.principal.asset.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert.event.principal.asset.platformSoftware.platform | The platform operating system. | keyword |
| google_secops.alert.event.principal.asset.platformSoftware.platformPatchLevel | The platform software patch level ( e.g. "Build 17134.48", "SP1"). | keyword |
| google_secops.alert.event.principal.asset.platformSoftware.platformVersion | The platform software version ( e.g. "Microsoft Windows 1803"). | keyword |
| google_secops.alert.event.principal.asset.productObjectId | A vendor-specific identifier to uniquely identify the entity (a GUID or similar). | keyword |
| google_secops.alert.event.principal.asset.software.version | The version of the software. | keyword |
| google_secops.alert.event.principal.asset.type | The type of the asset (e.g. workstation or laptop or server). | keyword |
| google_secops.alert.event.principal.assetId | The asset ID. | keyword |
| google_secops.alert.event.principal.cloud.environment | The Cloud environment. | keyword |
| google_secops.alert.event.principal.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.group.groupDisplayName | Group display name. e.g. "Finance". | keyword |
| google_secops.alert.event.principal.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert.event.principal.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert.event.principal.ipGeoArtifact.ip | IP address of the artifact. | ip |
| google_secops.alert.event.principal.ipGeoArtifact.location.countryOrRegion | The country or region. | keyword |
| google_secops.alert.event.principal.ipGeoArtifact.location.regionCoordinates.lat | The latitude in degrees. . | double |
| google_secops.alert.event.principal.ipGeoArtifact.location.regionCoordinates.lon | The longitude in degrees. . | double |
| google_secops.alert.event.principal.ipGeoArtifact.location.state | The state. | keyword |
| google_secops.alert.event.principal.ipGeoArtifact.network.asn | Autonomous system number. | keyword |
| google_secops.alert.event.principal.ipGeoArtifact.network.carrierName | Carrier identification. | keyword |
| google_secops.alert.event.principal.labels.key |  | keyword |
| google_secops.alert.event.principal.labels.value |  | keyword |
| google_secops.alert.event.principal.location.countryOrRegion | The country or region. | keyword |
| google_secops.alert.event.principal.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert.event.principal.platform | Platform. | keyword |
| google_secops.alert.event.principal.port | Source or destination network port number when a specific network connection is described within an event. | long |
| google_secops.alert.event.principal.process.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.principal.process.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.process.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.file.names | Names fields. | keyword |
| google_secops.alert.event.principal.process.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.file.signatureInfo.sigcheck.signers.name | Common name of the signers/certificate. The order of the signers matters. Each element is a higher level authority, the last being the root authority. | keyword |
| google_secops.alert.event.principal.process.file.signatureInfo.sigcheck.verificationMessage | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert.event.principal.process.parentProcess.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.principal.process.parentProcess.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.process.parentProcess.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.file.names | Names fields. | keyword |
| google_secops.alert.event.principal.process.parentProcess.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.file.signatureInfo.sigcheck.signers.name | Common name of the signers/certificate. The order of the signers matters. Each element is a higher level authority, the last being the root authority. | keyword |
| google_secops.alert.event.principal.process.parentProcess.file.signatureInfo.sigcheck.verificationMessage | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.file.names | Names fields. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.file.signatureInfo.sigcheck.verificationMessage | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.file.names | Names fields. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.file.signatureInfo.sigcheck.verificationMessage | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.file.names | Names fields. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.file.signatureInfo.sigcheck.verificationMessage | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.pid | The process ID. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.parentProcess.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.pid | The process ID. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.parentProcess.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.pid | The process ID. | keyword |
| google_secops.alert.event.principal.process.parentProcess.parentProcess.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.principal.process.parentProcess.pid | The process ID. | long |
| google_secops.alert.event.principal.process.parentProcess.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.principal.process.pid | The process ID. | long |
| google_secops.alert.event.principal.process.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.principal.processAncestors.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.file.names | Names fields. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.file.signatureInfo.sigcheck.verificationMessage | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.pid | The process ID. | keyword |
| google_secops.alert.event.principal.processAncestors.parentProcess.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.principal.processAncestors.pid | The process ID. | keyword |
| google_secops.alert.event.principal.registry.registryKey | Registry key associated with an application or system component (e.g., HKEY_, HKCU\Environment...). | keyword |
| google_secops.alert.event.principal.registry.registryValueName | Name of the registry value associated with an application or system component (e.g. TEMP). | keyword |
| google_secops.alert.event.principal.resource.attribute.cloud.project.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert.event.principal.resource.attribute.cloud.project.resourceSubtype | Resource sub-type (e.g. "BigQuery", "Bigtable"). | keyword |
| google_secops.alert.event.principal.resource.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert.event.principal.url | The URL. | keyword |
| google_secops.alert.event.principal.user.attribute.labels.key |  | keyword |
| google_secops.alert.event.principal.user.attribute.labels.value |  | keyword |
| google_secops.alert.event.principal.user.attribute.permissions.name | Name of the permission (e.g. chronicle.analyst.updateRule). | keyword |
| google_secops.alert.event.principal.user.attribute.permissions.type | Type of the permission. | keyword |
| google_secops.alert.event.principal.user.attribute.roles.description | System role description for user. | keyword |
| google_secops.alert.event.principal.user.attribute.roles.name | System role name for user. | keyword |
| google_secops.alert.event.principal.user.emailAddresses | Email addresses of the user. | keyword |
| google_secops.alert.event.principal.user.groupIdentifiers | Product object identifiers of the group(s) the user belongs to A vendor-specific identifier to uniquely identify the group(s) the user belongs to (a GUID, LDAP OID, or similar). | keyword |
| google_secops.alert.event.principal.user.productObjectId | A vendor-specific identifier to uniquely identify the entity (e.g. a GUID, LDAP, OID, or similar). | keyword |
| google_secops.alert.event.principal.user.userDisplayName | The display name of the user (e.g. "John Locke"). | keyword |
| google_secops.alert.event.principal.user.userid | The ID of the user. | keyword |
| google_secops.alert.event.principal.user.windowsSid | The Microsoft Windows SID of the user. | keyword |
| google_secops.alert.event.securityResult.about.resource.name |  | keyword |
| google_secops.alert.event.securityResult.about.user.attribute.roles.name | System role name for user. | keyword |
| google_secops.alert.event.securityResult.about.user.emailAddresses | Email addresses of the user. | keyword |
| google_secops.alert.event.securityResult.action | Actions taken for this event. | keyword |
| google_secops.alert.event.securityResult.alertState | The alerting types of this security result. | keyword |
| google_secops.alert.event.securityResult.category | The security category. | keyword |
| google_secops.alert.event.securityResult.categoryDetails | For vendor-specific categories. For web categorization, put type in here such as "gambling" or "porn". | keyword |
| google_secops.alert.event.securityResult.description | A human readable description (e.g. "user password was wrong").' | keyword |
| google_secops.alert.event.securityResult.detectionFields.key |  | keyword |
| google_secops.alert.event.securityResult.detectionFields.value |  | keyword |
| google_secops.alert.event.securityResult.firstDiscoveredTime | First time the IoC threat was discovered in the provider. | date |
| google_secops.alert.event.securityResult.priority | The priority of the result. | keyword |
| google_secops.alert.event.securityResult.priorityDetails | Vendor-specific information about the security result priority. | keyword |
| google_secops.alert.event.securityResult.ruleId | A vendor-specific ID and name for a rule, varying by observerer type (e.g. "08123", "5d2b44d0-5ef6-40f5-a704-47d61d3babbe"). | keyword |
| google_secops.alert.event.securityResult.ruleLabels.key |  | keyword |
| google_secops.alert.event.securityResult.ruleLabels.value |  | keyword |
| google_secops.alert.event.securityResult.ruleName | Name of the security rule (e.g. "BlockInboundToOracle"). | keyword |
| google_secops.alert.event.securityResult.ruleType | The type of security rule. | keyword |
| google_secops.alert.event.securityResult.severity | The severity of the result. | keyword |
| google_secops.alert.event.securityResult.severityDetails | Vendor-specific severity. | keyword |
| google_secops.alert.event.securityResult.summary | A human readable summary (e.g. "failed login occurred"). | keyword |
| google_secops.alert.event.securityResult.threatId | Vendor-specific ID for a threat. | keyword |
| google_secops.alert.event.securityResult.threatIdNamespace | The attribute threat_id_namespace qualifies threat_id with an ID namespace to get an unique ID. The attribute threat_id by itself is not unique across Google SecOps as it is a vendor specific ID. | keyword |
| google_secops.alert.event.securityResult.threatName | A vendor-assigned classification common across multiple customers (e.g. "W32/File-A", "Slammer"). | keyword |
| google_secops.alert.event.securityResult.urlBackToProduct | URL that takes the user to the source product console for this event. | keyword |
| google_secops.alert.event.src.asset.assetId | The asset ID. | keyword |
| google_secops.alert.event.src.asset.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert.event.src.asset.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert.event.src.asset.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert.event.src.assetId | The asset ID. | keyword |
| google_secops.alert.event.src.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.src.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.src.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.src.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert.event.src.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert.event.src.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert.event.src.process.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.src.user.emailAddresses | Email addresses of the user. | keyword |
| google_secops.alert.event.src.user.productObjectId | A vendor-specific identifier to uniquely identify the entity (e.g. a GUID, LDAP, OID, or similar). | keyword |
| google_secops.alert.event.src.user.userid | The ID of the user. | keyword |
| google_secops.alert.event.src.user.windowsSid | The Microsoft Windows SID of the user. | keyword |
| google_secops.alert.event.target.application | The name of an application or service. Some SSO solutions only capture the name of a target application such as "Atlassian" or "Google". | keyword |
| google_secops.alert.event.target.asset.assetId | The asset ID. Value must contain the ':' character. For example, cs:abcdd23434. | keyword |
| google_secops.alert.event.target.asset.hostname | Asset hostname or domain name field. | keyword |
| google_secops.alert.event.target.asset.ip | A list of IP addresses associated with an asset. | ip |
| google_secops.alert.event.target.asset.mac | List of MAC addresses associated with an asset. | keyword |
| google_secops.alert.event.target.assetId | The asset ID. | keyword |
| google_secops.alert.event.target.cloud.environment | The Cloud environment. | keyword |
| google_secops.alert.event.target.cloud.project.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert.event.target.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.target.file.lastModificationTime | Timestamp when the file was last updated. | date |
| google_secops.alert.event.target.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.target.file.names | Names fields. | keyword |
| google_secops.alert.event.target.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.target.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert.event.target.file.size | The size of the file in bytes. | keyword |
| google_secops.alert.event.target.group.groupDisplayName | Group display name. e.g. "Finance". | keyword |
| google_secops.alert.event.target.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert.event.target.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert.event.target.labels.key |  | keyword |
| google_secops.alert.event.target.labels.value |  | keyword |
| google_secops.alert.event.target.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert.event.target.port | Source or destination network port number when a specific network connection is described within an event. | long |
| google_secops.alert.event.target.process.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.target.process.file.firstSeenTime | Timestamp the file was first seen in the customer's environment. | date |
| google_secops.alert.event.target.process.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.target.process.file.lastModificationTime | Timestamp when the file was last updated. | date |
| google_secops.alert.event.target.process.parentProcess.commandLine | The command line command that created the process. | keyword |
| google_secops.alert.event.target.process.parentProcess.file.fullPath | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert.event.target.process.pid | The process ID. | keyword |
| google_secops.alert.event.target.process.productSpecificProcessId | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert.event.target.registry.registryKey | Registry key associated with an application or system component (e.g., HKEY_, HKCU\Environment...). | keyword |
| google_secops.alert.event.target.registry.registryValueData | Data associated with a registry value (e.g. %USERPROFILE%\Local Settings\Temp). | keyword |
| google_secops.alert.event.target.registry.registryValueName | Name of the registry value associated with an application or system component (e.g. TEMP). | keyword |
| google_secops.alert.event.target.resource.attribute.labels.key |  | keyword |
| google_secops.alert.event.target.resource.attribute.labels.value |  | keyword |
| google_secops.alert.event.target.resource.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert.event.target.resource.productObjectId | A vendor-specific identifier to uniquely identify the entity (a GUID, OID, or similar). | keyword |
| google_secops.alert.event.target.resource.resourceType | Resource type. | keyword |
| google_secops.alert.event.target.resourceAncestors.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert.event.target.resourceAncestors.productObjectId | A vendor-specific identifier to uniquely identify the entity (a GUID, OID, or similar). | keyword |
| google_secops.alert.event.target.user.emailAddresses | Email addresses of the user. | keyword |
| google_secops.alert.event.target.user.groupIdentifiers | Product object identifiers of the group(s) the user belongs to A vendor-specific identifier to uniquely identify the group(s) the user belongs to (a GUID, LDAP OID, or similar). | keyword |
| google_secops.alert.event.target.user.productObjectId | A vendor-specific identifier to uniquely identify the entity (e.g. a GUID, LDAP, OID, or similar). | keyword |
| google_secops.alert.event.target.user.userDisplayName | The display name of the user (e.g. "John Locke"). | keyword |
| google_secops.alert.event.target.user.userid | The ID of the user. | keyword |
| google_secops.alert.event.target.user.windowsSid | The Microsoft Windows SID of the user. | keyword |
| google_secops.alert.friendly_name | Alert Rule Name. | keyword |
| google_secops.alert.id | Identifier for the detection. Same as "detection_id". | keyword |
| google_secops.alert.label | The variable a given set of UDM events belongs to. | keyword |
| google_secops.alert.timeWindow.endTime | String representing the end of the time window in which the detection was found, in RFC 3339 format. | date |
| google_secops.alert.timeWindow.startTime | String representing the start of the time window in which the detection was found, in RFC 3339 format. | date |
| google_secops.alert.type | Type of detection (type is always `RULE_DETECTION`). | keyword |
| input.type | Type of Filebeat input. | keyword |
| log.offset | Log offset. | long |
| tags | User defined tags. | keyword |


### Alert v2

This is the `alert_v2` dataset.

#### Example

An example event for `alert_v2` looks as following:

```json
{
    "@timestamp": "2026-07-10T11:00:00.000Z",
    "ecs": {
        "version": "8.17.0"
    },
    "event": {
        "created": "2026-07-10T10:55:52.129Z",
        "end": "2026-07-10T11:00:00.000Z",
        "id": "de_0bf8b566-6501-ec41-b9a7-d0140edd6a79",
        "kind": "alert",
        "original": "{\"createdTime\": \"2026-07-10T10:55:52.129834Z\",\"detection\": [{\"alertState\": \"ALERTING\",\"description\": \"Detects events where source or principal IP matches a malicious or suspicious IP in GreyNoise intelligence.\",\"detectionFields\": [{\"key\": \"correlation_ip\",\"value\": \"1.128.0.0\"}],\"outcomes\": [{\"key\": \"principal_ip\",\"value\": \"1.128.0.0\"},{\"key\": \"principal_hostname\"},{\"key\": \"principal_user_userid\"},{\"key\": \"principal_mac\"},{\"key\": \"source_ip\"},{\"key\": \"source_hostname\"},{\"key\": \"source_user_userid\"},{\"key\": \"source_mac\"},{\"key\": \"target_ip\"},{\"key\": \"target_hostname\"},{\"key\": \"target_user_userid\"},{\"key\": \"target_mac\"}],\"riskScore\": 40,\"ruleId\": \"ru_83168d14-6ada-4f44-b802-b531de16a80b\",\"ruleLabels\": [{\"key\": \"author\",\"value\": \"GreyNoise Intelligence\"},{\"key\": \"description\",\"value\": \"Detects events where source or principal IP matches a malicious or suspicious IP in GreyNoise intelligence.\"},{\"key\": \"priority\",\"value\": \"Medium\"},{\"key\": \"rule_name\",\"value\": \"GreyNoise Intelligence IP Match\"},{\"key\": \"severity\",\"value\": \"High\"},{\"key\": \"tags\",\"value\": \"cloud security, threat intelligence\"}],\"ruleName\": \"greynoise_intelligence_ip_match\",\"ruleType\": \"MULTI_EVENT\",\"ruleVersion\": \"ru_83168d14-6ada-4f44-b802-b531de16a80b@v_1765958150_356360000\",\"urlBackToProduct\": \"https://chronicle.example.com/alert?alertId=de_0bf8b566-6501-ec41-b9a7-d0140edd6a79\"}],\"detectionTime\": \"2026-07-10T11:00:00Z\",\"event\": {\"about\": [{\"labels\": [{\"key\": \"watchlistsMatchedByType_id\",\"value\": \"3557389\"},{\"key\": \"watchlistsMatchedByType_name\",\"value\": \"Cyber Alerts\"},{\"key\": \"watchlistsMatchedByType_type\",\"value\": \"topics\"},{\"key\": \"watchlistsMatchedByType_userProperties_uiListType\",\"value\": \"CYBER\"}]},{\"labels\": [{\"key\": \"alertType_color\",\"value\": \"FFBB05\"},{\"key\": \"alertType_id\",\"value\": \"urgent\"},{\"key\": \"alertType_name\",\"value\": \"Urgent\"},{\"key\": \"availableRelatedAlerts\",\"value\": \"0\"},{\"key\": \"eventVolume\",\"value\": \"0\"},{\"key\": \"headerColor\",\"value\": \"FFFFAD\"},{\"key\": \"headerLabel\",\"value\": \"Urgent\"}]}],\"metadata\": {\"baseLabels\": {\"allowScopedAccess\": true,\"logTypes\": [\"DATAMINR_ALERT\"]},\"description\": \"Spike detected in malicious IPs attempting to exploit CVE-2017-20149.\",\"enrichmentLabels\": {\"allowScopedAccess\": true},\"eventTimestamp\": \"2026-07-10T10:26:46.872Z\",\"eventType\": \"SCAN_HOST\",\"id\": \"AAAAAC4+egHJ1IF+LhG0dL+Ogx8AAAAABgAAABsAAAA=\",\"ingestedTimestamp\": \"2026-07-10T10:40:06.231965Z\",\"logType\": \"DATAMINR_ALERT\",\"productLogId\": \"673882874984150889046821-1783677840000-1\",\"urlBackToProduct\": \"https://app.dataminr.com/#alertDetail/5/673882874984150889046821-1783677840000-1\"},\"principal\": {\"asset\": {\"ip\": [\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"1.128.0.0\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\"]},\"ip\": [\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"1.128.0.0\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\",\"81.2.69.142\",\"81.2.69.144\",\"81.2.69.192\",\"1.128.0.0\",\"175.16.199.0\",\"216.160.83.56\"],\"ipGeoArtifact\": [{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Pakistan\",\"regionCoordinates\": {\"latitude\": 31.170406299999996,\"longitude\": 72.7097161},\"state\": \"Punjab\"},\"network\": {\"asn\": \"23674\",\"carrierName\": \"nayatel (pvt) ltd\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Cameroon\",\"regionCoordinates\": {\"latitude\": 4.1682138,\"longitude\": 10.0807298},\"state\": \"Littoral Region\"},\"network\": {\"asn\": \"14593\",\"carrierName\": \"space exploration technologies corporation\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Indonesia\",\"regionCoordinates\": {\"latitude\": -6.180495,\"longitude\": 106.8283415},\"state\": \"Jakarta\"},\"network\": {\"asn\": \"9326\",\"carrierName\": \"pt centrin utama\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Iran\",\"regionCoordinates\": {\"latitude\": 35.5501285,\"longitude\": 51.5150077},\"state\": \"Tehran Province\"},\"network\": {\"asn\": \"5484\",\"carrierName\": \"shabakeh gostar shahriyar co. (ltd.)\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"Puerto Rico\",\"regionCoordinates\": {\"latitude\": 18.3863026,\"longitude\": -66.058249},\"state\": \"San Juan\"},\"network\": {\"asn\": \"11992\",\"carrierName\": \"liberty mobile puerto rico inc.\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -17.930177999999998,\"longitude\": -43.7908453},\"state\": \"State of Minas Gerais\"},\"network\": {\"asn\": \"263530\",\"carrierName\": \"microsol informatica ltda - me\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 19.7514798,\"longitude\": 75.7138884},\"state\": \"Maharashtra\"},\"network\": {\"asn\": \"55836\",\"carrierName\": \"reliance jio infocomm limited\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Cambodia\",\"regionCoordinates\": {\"latitude\": 11.5448729,\"longitude\": 104.8921668},\"state\": \"Phnom Penh\"},\"network\": {\"asn\": \"23673\",\"carrierName\": \"cogetel online cambodia isp\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -5.4983977,\"longitude\": -39.320624099999996},\"state\": \"Cear\\u00e1\"},\"network\": {\"asn\": \"28368\",\"carrierName\": \"sobralnet servicos e telecomunicacoes ltda - me\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"China\",\"regionCoordinates\": {\"latitude\": 39.904520399999996,\"longitude\": 116.4072503},\"state\": \"Beijing\"},\"network\": {\"asn\": \"56044\",\"carrierName\": \"china mobile communications corporation\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"China\",\"regionCoordinates\": {\"latitude\": 31.2303699,\"longitude\": 121.4737},\"state\": \"Shanghai\"},\"network\": {\"asn\": \"4812\",\"carrierName\": \"china telecom (group)\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"Vietnam\",\"regionCoordinates\": {\"latitude\": 21.022800099999998,\"longitude\": 105.68817879999999},\"state\": \"Ha Noi\"},\"network\": {\"asn\": \"18403\",\"carrierName\": \"fpt telecom company\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Cambodia\",\"regionCoordinates\": {\"latitude\": 11.5448729,\"longitude\": 104.8921668},\"state\": \"Phnom Penh\"},\"network\": {\"asn\": \"24492\",\"carrierName\": \"wicam corporation ltd.\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Colombia\",\"regionCoordinates\": {\"latitude\": 6.2476598,\"longitude\": -75.565816},\"state\": \"Antioquia\"},\"network\": {\"asn\": \"13489\",\"carrierName\": \"une epm telecomunicaciones s.a.\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"T\\u00fcrkiye\",\"regionCoordinates\": {\"latitude\": 38.3591693,\"longitude\": 27.2676116},\"state\": \"\\u0130zmir\"},\"network\": {\"asn\": \"34984\",\"carrierName\": \"tellcom iletisim hizmetleri a.s.\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Italy\",\"regionCoordinates\": {\"latitude\": 43.5671153,\"longitude\": 10.980700299999999},\"state\": \"Tuscany\"},\"network\": {\"asn\": \"200100\",\"carrierName\": \"t&t tecnologie e telecomunicazioni srl\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"Cambodia\",\"regionCoordinates\": {\"latitude\": 10.7581899,\"longitude\": 103.82162609999999},\"state\": \"Sihanoukville\"},\"network\": {\"asn\": \"9902\",\"carrierName\": \"neocomisp limited iptx transit and network service provider in cambodia\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Iraq\",\"regionCoordinates\": {\"latitude\": 33.3152618,\"longitude\": 44.366065299999995},\"state\": \"Baghdad Governorate\"},\"network\": {\"asn\": \"57588\",\"carrierName\": \"hayat for internet & communication\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 27.023803599999997,\"longitude\": 74.21793260000001},\"state\": \"Rajasthan\"},\"network\": {\"asn\": \"141293\",\"carrierName\": \"rajasthan internet hub pvt. ltd.\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Indonesia\",\"regionCoordinates\": {\"latitude\": -7.5360639,\"longitude\": 112.2384017},\"state\": \"East Java\"},\"network\": {\"asn\": \"7713\",\"carrierName\": \"pt telekomunikasi indonesia\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"Italy\",\"regionCoordinates\": {\"latitude\": 37.3979297,\"longitude\": 14.658782100000002},\"state\": \"Sicily\"},\"network\": {\"asn\": \"198380\",\"carrierName\": \"vincenza occhipinti trading as nova quadri s.a.s.\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Indonesia\",\"regionCoordinates\": {\"latitude\": -6.180495,\"longitude\": 106.8283415},\"state\": \"Jakarta\"},\"network\": {\"asn\": \"9794\",\"carrierName\": \"core mediatech\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Vietnam\",\"regionCoordinates\": {\"latitude\": 21.022800099999998,\"longitude\": 105.68817879999999},\"state\": \"Ha Noi\"},\"network\": {\"asn\": \"7552\",\"carrierName\": \"viettel group\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Uganda\",\"regionCoordinates\": {\"latitude\": 0.2540775,\"longitude\": 31.992807799999994},\"state\": \"Central Region\"},\"network\": {\"asn\": \"21491\",\"carrierName\": \"uganda telecom\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Bangladesh\",\"regionCoordinates\": {\"latitude\": 23.9535742,\"longitude\": 90.14949879999999},\"state\": \"Dhaka Division\"},\"network\": {\"asn\": \"63996\",\"carrierName\": \"mazeda networks limited\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Colombia\",\"regionCoordinates\": {\"latitude\": 6.2476598,\"longitude\": -75.565816},\"state\": \"Antioquia\"},\"network\": {\"asn\": \"13489\",\"carrierName\": \"une epm telecomunicaciones s.a.\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Bangladesh\",\"regionCoordinates\": {\"latitude\": 24.704981099999998,\"longitude\": 91.67606909999999},\"state\": \"Sylhet Division\"},\"network\": {\"asn\": \"24342\",\"carrierName\": \"bracnet limited\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"Russia\",\"regionCoordinates\": {\"latitude\": 58.8231929,\"longitude\": 56.5872481},\"state\": \"Perm Krai\"},\"network\": {\"asn\": \"12768\",\"carrierName\": \"jsc er-telecom holding\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -8.8137173,\"longitude\": -36.954107},\"state\": \"State of Pernambuco\"},\"network\": {\"asn\": \"266921\",\"carrierName\": \"mundial station\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Bangladesh\",\"regionCoordinates\": {\"latitude\": 23.1793157,\"longitude\": 91.9881527},\"state\": \"Chittagong Division\"},\"network\": {\"asn\": \"9832\",\"carrierName\": \"isn internet service provider\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 29.0587757,\"longitude\": 76.085601},\"state\": \"Haryana\"},\"network\": {\"asn\": \"153252\",\"carrierName\": \"starlinks\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -8.8137173,\"longitude\": -36.954107},\"state\": \"State of Pernambuco\"},\"network\": {\"asn\": \"266921\",\"carrierName\": \"mundial station\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Nepal\",\"regionCoordinates\": {\"latitude\": 27.6624958,\"longitude\": 85.4375574},\"state\": \"Bagmati Province\"},\"network\": {\"asn\": \"4007\",\"carrierName\": \"subisu cablenet (pvt) ltd\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -11.4098737,\"longitude\": -41.2808577},\"state\": \"State of Bahia\"},\"network\": {\"asn\": \"61727\",\"carrierName\": \"spn telecom\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -17.930177999999998,\"longitude\": -43.7908453},\"state\": \"State of Minas Gerais\"},\"network\": {\"asn\": \"262805\",\"carrierName\": \"rede minas telecom ltda\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Indonesia\",\"regionCoordinates\": {\"latitude\": -6.180495,\"longitude\": 106.8283415},\"state\": \"Jakarta\"},\"network\": {\"asn\": \"58369\",\"carrierName\": \"fiber networks indonesia\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -19.1834229,\"longitude\": -40.3088626},\"state\": \"State of Esp\\u00edrito Santo\"},\"network\": {\"asn\": \"267159\",\"carrierName\": \"ltnet telecomunicacoes eirele me\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Cambodia\",\"regionCoordinates\": {\"latitude\": 11.5448729,\"longitude\": 104.8921668},\"state\": \"Phnom Penh\"},\"network\": {\"asn\": \"38235\",\"carrierName\": \"angkor data communication\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 27.570588599999997,\"longitude\": 80.0981869},\"state\": \"Uttar Pradesh\"},\"network\": {\"asn\": \"139508\",\"carrierName\": \"wide netcom india\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Brazil\",\"regionCoordinates\": {\"latitude\": -21.2922457,\"longitude\": -50.342843099999996},\"state\": \"State of S\\u00e3o Paulo\"},\"network\": {\"asn\": \"266200\",\"carrierName\": \"ex2net telecomunica oes - ltda\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Iran\",\"regionCoordinates\": {\"latitude\": 35.8898715,\"longitude\": 50.8456323},\"state\": \"Alborz Province\"},\"network\": {\"asn\": \"56703\",\"carrierName\": \"mehr ava gostar parsian information engineering co. ltd\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Nepal\",\"regionCoordinates\": {\"latitude\": 27.6624958,\"longitude\": 85.4375574},\"state\": \"Bagmati Province\"},\"network\": {\"asn\": \"4007\",\"carrierName\": \"subisu cablenet (pvt) ltd\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Philippines\",\"regionCoordinates\": {\"latitude\": 14.609053699999997,\"longitude\": 121.02225650000001},\"state\": \"Metro Manila\"},\"network\": {\"asn\": \"17970\",\"carrierName\": \"skybroadband skycable corporation\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Cameroon\",\"regionCoordinates\": {\"latitude\": 4.6298411,\"longitude\": 11.7068294},\"state\": \"Centre Region\"},\"network\": {\"asn\": \"36955\",\"carrierName\": \"matrix telecoms sa\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Armenia\",\"regionCoordinates\": {\"latitude\": 40.1794197,\"longitude\": 44.5408414},\"state\": \"Yerevan\"},\"network\": {\"asn\": \"201986\",\"carrierName\": \"arpinet llc\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 27.023803599999997,\"longitude\": 74.21793260000001},\"state\": \"Rajasthan\"},\"network\": {\"asn\": \"55507\",\"carrierName\": \"tejays dynamic limited\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Bangladesh\",\"regionCoordinates\": {\"latitude\": 23.9535742,\"longitude\": 90.14949879999999},\"state\": \"Dhaka Division\"},\"network\": {\"asn\": \"136224\",\"carrierName\": \"city online ltd.\"}},{\"ip\": \"81.2.69.144\",\"location\": {\"countryOrRegion\": \"Bangladesh\",\"regionCoordinates\": {\"latitude\": 23.9535742,\"longitude\": 90.14949879999999},\"state\": \"Dhaka Division\"},\"network\": {\"asn\": \"134153\",\"carrierName\": \"xplore net bd\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Poland\",\"regionCoordinates\": {\"latitude\": 53.465789099999995,\"longitude\": 15.182258099999999},\"state\": \"West Pomeranian Voivodeship\"},\"network\": {\"asn\": \"31608\",\"carrierName\": \"gawex media sp. z o. o.\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Bulgaria\",\"regionCoordinates\": {\"latitude\": 42.7570109,\"longitude\": 23.4504683},\"state\": \"Sofia City Province\"},\"network\": {\"asn\": \"8717\",\"carrierName\": \"a1 bulgaria ead\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"Indonesia\",\"regionCoordinates\": {\"latitude\": -7.150975,\"longitude\": 110.14025939999999},\"state\": \"Central Java\"},\"network\": {\"asn\": \"7713\",\"carrierName\": \"pt telekomunikasi indonesia\"}},{\"ip\": \"1.128.0.0\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 15.912899800000002,\"longitude\": 79.7399875},\"state\": \"Andhra Pradesh\"},\"network\": {\"asn\": \"131459\",\"carrierName\": \"88c  race course road  coimbatore 641018\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Tanzania\",\"regionCoordinates\": {\"latitude\": -6.9042365000000006,\"longitude\": 39.1959729},\"state\": \"Dar es Salam\"},\"network\": {\"asn\": \"30844\",\"carrierName\": \"liquid telecommunications ltd\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Kenya\",\"regionCoordinates\": {\"latitude\": -1.3106691,\"longitude\": 36.825027399999996},\"state\": \"Nairobi County\"},\"network\": {\"asn\": \"12455\",\"carrierName\": \"jambonet\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"Cambodia\",\"regionCoordinates\": {\"latitude\": 11.5448729,\"longitude\": 104.8921668},\"state\": \"Phnom Penh\"},\"network\": {\"asn\": \"23673\",\"carrierName\": \"cogetel online cambodia isp\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"China\",\"regionCoordinates\": {\"latitude\": 39.904520399999996,\"longitude\": 116.4072503},\"state\": \"Beijing\"},\"network\": {\"asn\": \"56044\",\"carrierName\": \"china mobile communications corporation\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 27.023803599999997,\"longitude\": 74.21793260000001},\"state\": \"Rajasthan\"},\"network\": {\"asn\": \"45433\",\"carrierName\": \"kappa internet services private limited\"}},{\"ip\": \"216.160.83.56\",\"location\": {\"countryOrRegion\": \"Eswatini\",\"regionCoordinates\": {\"latitude\": -26.785177299999997,\"longitude\": 31.810707899999997},\"state\": \"Lubombo Region\"},\"network\": {\"asn\": \"327750\",\"carrierName\": \"jenny internet (pty) ltd\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Cameroon\",\"regionCoordinates\": {\"latitude\": 4.6298411,\"longitude\": 11.7068294},\"state\": \"Centre Region\"},\"network\": {\"asn\": \"36905\",\"carrierName\": \"creolink communications\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 27.570588599999997,\"longitude\": 80.0981869},\"state\": \"Uttar Pradesh\"},\"network\": {\"asn\": \"136654\",\"carrierName\": \"krishna broadband\"}},{\"ip\": \"81.2.69.192\",\"location\": {\"countryOrRegion\": \"Bangladesh\",\"regionCoordinates\": {\"latitude\": 23.1793157,\"longitude\": 91.9881527},\"state\": \"Chittagong Division\"},\"network\": {\"asn\": \"23923\",\"carrierName\": \"agni systems limited\"}},{\"ip\": \"81.2.69.142\",\"location\": {\"countryOrRegion\": \"India\",\"regionCoordinates\": {\"latitude\": 11.127122499999999,\"longitude\": 78.6568942},\"state\": \"Tamil Nadu\"},\"network\": {\"asn\": \"17488\",\"carrierName\": \"hathway ip over cable internet\"}},{\"ip\": \"175.16.199.0\",\"location\": {\"countryOrRegion\": \"Colombia\",\"regionCoordinates\": {\"latitude\": 6.2476598,\"longitude\": -75.565816},\"state\": \"Antioquia\"},\"network\": {\"asn\": \"13489\",\"carrierName\": \"colombia m vil\"}}],\"labels\": [{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"value\": \"string\"},{\"key\": \"relatedTerms_cve\",\"value\": \"https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22cve%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22cve%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D\"},{\"key\": \"relatedTerms_detected\",\"value\": \"https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22detected%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22detected%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D\"},{\"key\": \"relatedTerms_exploit\",\"value\": \"https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22exploit%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22exploit%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D\"},{\"key\": \"relatedTerms_malicious\",\"value\": \"https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22malicious%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22malicious%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D\"},{\"key\": \"relatedTermsQueryURL\",\"value\": \"https://app.dataminr.com/#search-popup/search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22cve,detected,exploit,malicious%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22cve,detected,exploit,malicious%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D\"}],\"location\": {\"countryOrRegion\": \"Pakistan\"}},\"target\": {\"resource\": {\"attribute\": {\"labels\": [{\"key\": \"post_media_media_url\",\"value\": \"https://d17m4o70dfu80a.cloudfront.net/DuyBDHYMPa14hlstmLImTPCT2SY=.png?Expires=1786270980&Signature=ZR-PF7iW3ia5TppAdp80VEsIiWSGP5XxydngDCB-52Y2Og~WDNu26tUeZ2ZlcANLE0Q8bX7ofh5ded-T7UiC4ZDl7IienMcKm6jK6K4opd2rvJPdjFbxx3kyO3YmkcSPVbN~Ov5QRkhal9ozGjjz4GTmGfe022mxMToUIREa~hgPAa9Q9ltfKJ38kMEo7g7ObHi6r-m1XggrVK~ywlMcwOPEl0kAKDapRNf60nLilBzeABt~4IaG6QS3BJz5DA8CNgyZhduBruj-NjYMsiRQhkWBi9QN8GvVgTdhLahIZ9FDNXBe0sHenlPXoNm3UfCybmjoBsXvuqebCimQJiyeNw__&Key-Pair-Id=APKAJFPRXY5MHQNE5FVQ\"},{\"key\": \"post_media_url\",\"value\": \"https://d17m4o70dfu80a.cloudfront.net/DuyBDHYMPa14hlstmLImTPCT2SY=.png?Expires=1786270980&Signature=ZR-PF7iW3ia5TppAdp80VEsIiWSGP5XxydngDCB-52Y2Og~WDNu26tUeZ2ZlcANLE0Q8bX7ofh5ded-T7UiC4ZDl7IienMcKm6jK6K4opd2rvJPdjFbxx3kyO3YmkcSPVbN~Ov5QRkhal9ozGjjz4GTmGfe022mxMToUIREa~hgPAa9Q9ltfKJ38kMEo7g7ObHi6r-m1XggrVK~ywlMcwOPEl0kAKDapRNf60nLilBzeABt~4IaG6QS3BJz5DA8CNgyZhduBruj-NjYMsiRQhkWBi9QN8GvVgTdhLahIZ9FDNXBe0sHenlPXoNm3UfCybmjoBsXvuqebCimQJiyeNw__&Key-Pair-Id=APKAJFPRXY5MHQNE5FVQ\"}]}}}},\"id\": \"de_0bf8b566-6501-ec41-b9a7-d0140edd6a79\",\"label\": \"network\",\"timeWindow\": {\"endTime\": \"2026-07-10T11:00:00Z\",\"startTime\": \"2026-07-10T10:00:00Z\"},\"type\": \"RULE_DETECTION\"}",
        "risk_score": 40,
        "start": "2026-07-10T10:00:00.000Z"
    },
    "google_secops": {
        "alert_v2": {
            "detection": [
                {
                    "alert_state": "ALERTING",
                    "detection_fields": [
                        {
                            "key": "correlation_ip",
                            "value": "1.128.0.0"
                        }
                    ],
                    "outcomes": [
                        {
                            "key": "principal_ip",
                            "value": "1.128.0.0"
                        },
                        {
                            "key": "principal_hostname"
                        },
                        {
                            "key": "principal_user_userid"
                        },
                        {
                            "key": "principal_mac"
                        },
                        {
                            "key": "source_ip"
                        },
                        {
                            "key": "source_hostname"
                        },
                        {
                            "key": "source_user_userid"
                        },
                        {
                            "key": "source_mac"
                        },
                        {
                            "key": "target_ip"
                        },
                        {
                            "key": "target_hostname"
                        },
                        {
                            "key": "target_user_userid"
                        },
                        {
                            "key": "target_mac"
                        }
                    ],
                    "rule_labels": [
                        {
                            "key": "author",
                            "value": "GreyNoise Intelligence"
                        },
                        {
                            "key": "description",
                            "value": "Detects events where source or principal IP matches a malicious or suspicious IP in GreyNoise intelligence."
                        },
                        {
                            "key": "priority",
                            "value": "Medium"
                        },
                        {
                            "key": "rule_name",
                            "value": "GreyNoise Intelligence IP Match"
                        },
                        {
                            "key": "severity",
                            "value": "High"
                        },
                        {
                            "key": "tags",
                            "value": "cloud security, threat intelligence"
                        }
                    ],
                    "rule_type": "MULTI_EVENT",
                    "url_back_to_product": "https://chronicle.example.com/alert?alertId=de_0bf8b566-6501-ec41-b9a7-d0140edd6a79"
                }
            ],
            "event": {
                "about": [
                    {
                        "labels": [
                            {
                                "key": "watchlistsMatchedByType_id",
                                "value": "3557389"
                            },
                            {
                                "key": "watchlistsMatchedByType_name",
                                "value": "Cyber Alerts"
                            },
                            {
                                "key": "watchlistsMatchedByType_type",
                                "value": "topics"
                            },
                            {
                                "key": "watchlistsMatchedByType_userProperties_uiListType",
                                "value": "CYBER"
                            }
                        ]
                    },
                    {
                        "labels": [
                            {
                                "key": "alertType_color",
                                "value": "FFBB05"
                            },
                            {
                                "key": "alertType_id",
                                "value": "urgent"
                            },
                            {
                                "key": "alertType_name",
                                "value": "Urgent"
                            },
                            {
                                "key": "availableRelatedAlerts",
                                "value": "0"
                            },
                            {
                                "key": "eventVolume",
                                "value": "0"
                            },
                            {
                                "key": "headerColor",
                                "value": "FFFFAD"
                            },
                            {
                                "key": "headerLabel",
                                "value": "Urgent"
                            }
                        ]
                    }
                ],
                "metadata": {
                    "base_labels": {
                        "allow_scoped_access": true,
                        "log_types": [
                            "DATAMINR_ALERT"
                        ]
                    },
                    "enrichment_labels": {
                        "allow_scoped_access": true
                    },
                    "event_timestamp": "2026-07-10T10:26:46.872Z",
                    "event_type": "SCAN_HOST",
                    "id": "AAAAAC4+egHJ1IF+LhG0dL+Ogx8AAAAABgAAABsAAAA=",
                    "ingested_timestamp": "2026-07-10T10:40:06.231Z",
                    "log_type": "DATAMINR_ALERT",
                    "product_log_id": "673882874984150889046821-1783677840000-1",
                    "url_back_to_product": "https://app.dataminr.com/#alertDetail/5/673882874984150889046821-1783677840000-1"
                },
                "principal": {
                    "asset": {
                        "ip": [
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "1.128.0.0",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56",
                            "81.2.69.142",
                            "81.2.69.144",
                            "81.2.69.192",
                            "1.128.0.0",
                            "175.16.199.0",
                            "216.160.83.56"
                        ]
                    },
                    "ip_geo_artifact": [
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 31.170406299999996,
                                    "lon": 72.7097161
                                }
                            },
                            "network": {
                                "asn": "23674",
                                "carrier_name": "nayatel (pvt) ltd"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 4.1682138,
                                    "lon": 10.0807298
                                }
                            },
                            "network": {
                                "asn": "14593",
                                "carrier_name": "space exploration technologies corporation"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -6.180495,
                                    "lon": 106.8283415
                                }
                            },
                            "network": {
                                "asn": "9326",
                                "carrier_name": "pt centrin utama"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 35.5501285,
                                    "lon": 51.5150077
                                }
                            },
                            "network": {
                                "asn": "5484",
                                "carrier_name": "shabakeh gostar shahriyar co. (ltd.)"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 18.3863026,
                                    "lon": -66.058249
                                }
                            },
                            "network": {
                                "asn": "11992",
                                "carrier_name": "liberty mobile puerto rico inc."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -17.930177999999998,
                                    "lon": -43.7908453
                                }
                            },
                            "network": {
                                "asn": "263530",
                                "carrier_name": "microsol informatica ltda - me"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 19.7514798,
                                    "lon": 75.7138884
                                }
                            },
                            "network": {
                                "asn": "55836",
                                "carrier_name": "reliance jio infocomm limited"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 11.5448729,
                                    "lon": 104.8921668
                                }
                            },
                            "network": {
                                "asn": "23673",
                                "carrier_name": "cogetel online cambodia isp"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -5.4983977,
                                    "lon": -39.320624099999996
                                }
                            },
                            "network": {
                                "asn": "28368",
                                "carrier_name": "sobralnet servicos e telecomunicacoes ltda - me"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 39.904520399999996,
                                    "lon": 116.4072503
                                }
                            },
                            "network": {
                                "asn": "56044",
                                "carrier_name": "china mobile communications corporation"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 31.2303699,
                                    "lon": 121.4737
                                }
                            },
                            "network": {
                                "asn": "4812",
                                "carrier_name": "china telecom (group)"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 21.022800099999998,
                                    "lon": 105.68817879999999
                                }
                            },
                            "network": {
                                "asn": "18403",
                                "carrier_name": "fpt telecom company"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 11.5448729,
                                    "lon": 104.8921668
                                }
                            },
                            "network": {
                                "asn": "24492",
                                "carrier_name": "wicam corporation ltd."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 6.2476598,
                                    "lon": -75.565816
                                }
                            },
                            "network": {
                                "asn": "13489",
                                "carrier_name": "une epm telecomunicaciones s.a."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 38.3591693,
                                    "lon": 27.2676116
                                }
                            },
                            "network": {
                                "asn": "34984",
                                "carrier_name": "tellcom iletisim hizmetleri a.s."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 43.5671153,
                                    "lon": 10.980700299999999
                                }
                            },
                            "network": {
                                "asn": "200100",
                                "carrier_name": "t&t tecnologie e telecomunicazioni srl"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 10.7581899,
                                    "lon": 103.82162609999999
                                }
                            },
                            "network": {
                                "asn": "9902",
                                "carrier_name": "neocomisp limited iptx transit and network service provider in cambodia"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 33.3152618,
                                    "lon": 44.366065299999995
                                }
                            },
                            "network": {
                                "asn": "57588",
                                "carrier_name": "hayat for internet & communication"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 27.023803599999997,
                                    "lon": 74.21793260000001
                                }
                            },
                            "network": {
                                "asn": "141293",
                                "carrier_name": "rajasthan internet hub pvt. ltd."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -7.5360639,
                                    "lon": 112.2384017
                                }
                            },
                            "network": {
                                "asn": "7713",
                                "carrier_name": "pt telekomunikasi indonesia"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 37.3979297,
                                    "lon": 14.658782100000002
                                }
                            },
                            "network": {
                                "asn": "198380",
                                "carrier_name": "vincenza occhipinti trading as nova quadri s.a.s."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -6.180495,
                                    "lon": 106.8283415
                                }
                            },
                            "network": {
                                "asn": "9794",
                                "carrier_name": "core mediatech"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 21.022800099999998,
                                    "lon": 105.68817879999999
                                }
                            },
                            "network": {
                                "asn": "7552",
                                "carrier_name": "viettel group"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 0.2540775,
                                    "lon": 31.992807799999994
                                }
                            },
                            "network": {
                                "asn": "21491",
                                "carrier_name": "uganda telecom"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 23.9535742,
                                    "lon": 90.14949879999999
                                }
                            },
                            "network": {
                                "asn": "63996",
                                "carrier_name": "mazeda networks limited"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 6.2476598,
                                    "lon": -75.565816
                                }
                            },
                            "network": {
                                "asn": "13489",
                                "carrier_name": "une epm telecomunicaciones s.a."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 24.704981099999998,
                                    "lon": 91.67606909999999
                                }
                            },
                            "network": {
                                "asn": "24342",
                                "carrier_name": "bracnet limited"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 58.8231929,
                                    "lon": 56.5872481
                                }
                            },
                            "network": {
                                "asn": "12768",
                                "carrier_name": "jsc er-telecom holding"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -8.8137173,
                                    "lon": -36.954107
                                }
                            },
                            "network": {
                                "asn": "266921",
                                "carrier_name": "mundial station"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 23.1793157,
                                    "lon": 91.9881527
                                }
                            },
                            "network": {
                                "asn": "9832",
                                "carrier_name": "isn internet service provider"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 29.0587757,
                                    "lon": 76.085601
                                }
                            },
                            "network": {
                                "asn": "153252",
                                "carrier_name": "starlinks"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -8.8137173,
                                    "lon": -36.954107
                                }
                            },
                            "network": {
                                "asn": "266921",
                                "carrier_name": "mundial station"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 27.6624958,
                                    "lon": 85.4375574
                                }
                            },
                            "network": {
                                "asn": "4007",
                                "carrier_name": "subisu cablenet (pvt) ltd"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -11.4098737,
                                    "lon": -41.2808577
                                }
                            },
                            "network": {
                                "asn": "61727",
                                "carrier_name": "spn telecom"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -17.930177999999998,
                                    "lon": -43.7908453
                                }
                            },
                            "network": {
                                "asn": "262805",
                                "carrier_name": "rede minas telecom ltda"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -6.180495,
                                    "lon": 106.8283415
                                }
                            },
                            "network": {
                                "asn": "58369",
                                "carrier_name": "fiber networks indonesia"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -19.1834229,
                                    "lon": -40.3088626
                                }
                            },
                            "network": {
                                "asn": "267159",
                                "carrier_name": "ltnet telecomunicacoes eirele me"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 11.5448729,
                                    "lon": 104.8921668
                                }
                            },
                            "network": {
                                "asn": "38235",
                                "carrier_name": "angkor data communication"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 27.570588599999997,
                                    "lon": 80.0981869
                                }
                            },
                            "network": {
                                "asn": "139508",
                                "carrier_name": "wide netcom india"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -21.2922457,
                                    "lon": -50.342843099999996
                                }
                            },
                            "network": {
                                "asn": "266200",
                                "carrier_name": "ex2net telecomunica oes - ltda"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 35.8898715,
                                    "lon": 50.8456323
                                }
                            },
                            "network": {
                                "asn": "56703",
                                "carrier_name": "mehr ava gostar parsian information engineering co. ltd"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 27.6624958,
                                    "lon": 85.4375574
                                }
                            },
                            "network": {
                                "asn": "4007",
                                "carrier_name": "subisu cablenet (pvt) ltd"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 14.609053699999997,
                                    "lon": 121.02225650000001
                                }
                            },
                            "network": {
                                "asn": "17970",
                                "carrier_name": "skybroadband skycable corporation"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 4.6298411,
                                    "lon": 11.7068294
                                }
                            },
                            "network": {
                                "asn": "36955",
                                "carrier_name": "matrix telecoms sa"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 40.1794197,
                                    "lon": 44.5408414
                                }
                            },
                            "network": {
                                "asn": "201986",
                                "carrier_name": "arpinet llc"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 27.023803599999997,
                                    "lon": 74.21793260000001
                                }
                            },
                            "network": {
                                "asn": "55507",
                                "carrier_name": "tejays dynamic limited"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 23.9535742,
                                    "lon": 90.14949879999999
                                }
                            },
                            "network": {
                                "asn": "136224",
                                "carrier_name": "city online ltd."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 23.9535742,
                                    "lon": 90.14949879999999
                                }
                            },
                            "network": {
                                "asn": "134153",
                                "carrier_name": "xplore net bd"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 53.465789099999995,
                                    "lon": 15.182258099999999
                                }
                            },
                            "network": {
                                "asn": "31608",
                                "carrier_name": "gawex media sp. z o. o."
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 42.7570109,
                                    "lon": 23.4504683
                                }
                            },
                            "network": {
                                "asn": "8717",
                                "carrier_name": "a1 bulgaria ead"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -7.150975,
                                    "lon": 110.14025939999999
                                }
                            },
                            "network": {
                                "asn": "7713",
                                "carrier_name": "pt telekomunikasi indonesia"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 15.912899800000002,
                                    "lon": 79.7399875
                                }
                            },
                            "network": {
                                "asn": "131459",
                                "carrier_name": "88c  race course road  coimbatore 641018"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -6.9042365000000006,
                                    "lon": 39.1959729
                                }
                            },
                            "network": {
                                "asn": "30844",
                                "carrier_name": "liquid telecommunications ltd"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -1.3106691,
                                    "lon": 36.825027399999996
                                }
                            },
                            "network": {
                                "asn": "12455",
                                "carrier_name": "jambonet"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 11.5448729,
                                    "lon": 104.8921668
                                }
                            },
                            "network": {
                                "asn": "23673",
                                "carrier_name": "cogetel online cambodia isp"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 39.904520399999996,
                                    "lon": 116.4072503
                                }
                            },
                            "network": {
                                "asn": "56044",
                                "carrier_name": "china mobile communications corporation"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 27.023803599999997,
                                    "lon": 74.21793260000001
                                }
                            },
                            "network": {
                                "asn": "45433",
                                "carrier_name": "kappa internet services private limited"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": -26.785177299999997,
                                    "lon": 31.810707899999997
                                }
                            },
                            "network": {
                                "asn": "327750",
                                "carrier_name": "jenny internet (pty) ltd"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 4.6298411,
                                    "lon": 11.7068294
                                }
                            },
                            "network": {
                                "asn": "36905",
                                "carrier_name": "creolink communications"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 27.570588599999997,
                                    "lon": 80.0981869
                                }
                            },
                            "network": {
                                "asn": "136654",
                                "carrier_name": "krishna broadband"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 23.1793157,
                                    "lon": 91.9881527
                                }
                            },
                            "network": {
                                "asn": "23923",
                                "carrier_name": "agni systems limited"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 11.127122499999999,
                                    "lon": 78.6568942
                                }
                            },
                            "network": {
                                "asn": "17488",
                                "carrier_name": "hathway ip over cable internet"
                            }
                        },
                        {
                            "location": {
                                "region_coordinates": {
                                    "lat": 6.2476598,
                                    "lon": -75.565816
                                }
                            },
                            "network": {
                                "asn": "13489",
                                "carrier_name": "colombia m vil"
                            }
                        }
                    ],
                    "labels": [
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "value": "string"
                        },
                        {
                            "key": "relatedTerms_cve",
                            "value": "https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22cve%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22cve%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D"
                        },
                        {
                            "key": "relatedTerms_detected",
                            "value": "https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22detected%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22detected%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D"
                        },
                        {
                            "key": "relatedTerms_exploit",
                            "value": "https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22exploit%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22exploit%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D"
                        },
                        {
                            "key": "relatedTerms_malicious",
                            "value": "https://app.dataminr.com/app/core/corporate/search-popup.html#search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22malicious%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22malicious%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D"
                        },
                        {
                            "key": "relatedTermsQueryURL",
                            "value": "https://app.dataminr.com/#search-popup/search/%7B%22history%22%3A%5B%7B%22displayTitle%22%3A%22cve,detected,exploit,malicious%22%2C%22elements%22%3A%5B%7B%22topicName%22%3A%22cve,detected,exploit,malicious%22%2C%22type%22%3A%22string%22%2C%22topicId%22%3A-1%2C%22equitySymbol%22%3A%22%22%7D%5D%2C%22target%22%3A%22searchinput%22%2C%22type%22%3A%22complex%22%2C%22isEquity%22%3Afalse%2C%22topicId%22%3A-1%2C%22text%22%3A%22%22%7D%5D%7D/location/%7B%22center%22%3A%7B%22lat%22%3A14.43468021529728%2C%22lng%22%3A-65.91796875%2C%22zoom%22%3A2%7D%2C%22zoom%22%3A2%2C%22extent%22%3A%7B%22north%22%3A73.42842364106818%2C%22east%22%3A48.1640625%2C%22south%22%3A-62.91523303947613%2C%22west%22%3A-179.6484375%7D%7D"
                        }
                    ],
                    "location": {
                        "country_or_region": "Pakistan"
                    }
                },
                "target": {
                    "resource": {
                        "attribute": {
                            "labels": [
                                {
                                    "key": "post_media_media_url",
                                    "value": "https://d17m4o70dfu80a.cloudfront.net/DuyBDHYMPa14hlstmLImTPCT2SY=.png?Expires=1786270980&Signature=ZR-PF7iW3ia5TppAdp80VEsIiWSGP5XxydngDCB-52Y2Og~WDNu26tUeZ2ZlcANLE0Q8bX7ofh5ded-T7UiC4ZDl7IienMcKm6jK6K4opd2rvJPdjFbxx3kyO3YmkcSPVbN~Ov5QRkhal9ozGjjz4GTmGfe022mxMToUIREa~hgPAa9Q9ltfKJ38kMEo7g7ObHi6r-m1XggrVK~ywlMcwOPEl0kAKDapRNf60nLilBzeABt~4IaG6QS3BJz5DA8CNgyZhduBruj-NjYMsiRQhkWBi9QN8GvVgTdhLahIZ9FDNXBe0sHenlPXoNm3UfCybmjoBsXvuqebCimQJiyeNw__&Key-Pair-Id=APKAJFPRXY5MHQNE5FVQ"
                                },
                                {
                                    "key": "post_media_url",
                                    "value": "https://d17m4o70dfu80a.cloudfront.net/DuyBDHYMPa14hlstmLImTPCT2SY=.png?Expires=1786270980&Signature=ZR-PF7iW3ia5TppAdp80VEsIiWSGP5XxydngDCB-52Y2Og~WDNu26tUeZ2ZlcANLE0Q8bX7ofh5ded-T7UiC4ZDl7IienMcKm6jK6K4opd2rvJPdjFbxx3kyO3YmkcSPVbN~Ov5QRkhal9ozGjjz4GTmGfe022mxMToUIREa~hgPAa9Q9ltfKJ38kMEo7g7ObHi6r-m1XggrVK~ywlMcwOPEl0kAKDapRNf60nLilBzeABt~4IaG6QS3BJz5DA8CNgyZhduBruj-NjYMsiRQhkWBi9QN8GvVgTdhLahIZ9FDNXBe0sHenlPXoNm3UfCybmjoBsXvuqebCimQJiyeNw__&Key-Pair-Id=APKAJFPRXY5MHQNE5FVQ"
                                }
                            ]
                        }
                    }
                }
            },
            "friendly_name": "greynoise_intelligence_ip_match",
            "label": "network",
            "type": "RULE_DETECTION"
        }
    },
    "host": {
        "geo": {
            "country_name": [
                "Pakistan",
                "Cameroon",
                "Indonesia",
                "Iran",
                "Puerto Rico",
                "Brazil",
                "India",
                "Cambodia",
                "China",
                "Vietnam",
                "Colombia",
                "T\u00fcrkiye",
                "Italy",
                "Iraq",
                "Uganda",
                "Bangladesh",
                "Russia",
                "Nepal",
                "Philippines",
                "Armenia",
                "Poland",
                "Bulgaria",
                "Tanzania",
                "Kenya",
                "Eswatini"
            ],
            "location": [
                {
                    "lat": 31.170406299999996,
                    "lon": 72.7097161
                },
                {
                    "lat": 4.1682138,
                    "lon": 10.0807298
                },
                {
                    "lat": -6.180495,
                    "lon": 106.8283415
                },
                {
                    "lat": 35.5501285,
                    "lon": 51.5150077
                },
                {
                    "lat": 18.3863026,
                    "lon": -66.058249
                },
                {
                    "lat": -17.930177999999998,
                    "lon": -43.7908453
                },
                {
                    "lat": 19.7514798,
                    "lon": 75.7138884
                },
                {
                    "lat": 11.5448729,
                    "lon": 104.8921668
                },
                {
                    "lat": -5.4983977,
                    "lon": -39.320624099999996
                },
                {
                    "lat": 39.904520399999996,
                    "lon": 116.4072503
                },
                {
                    "lat": 31.2303699,
                    "lon": 121.4737
                },
                {
                    "lat": 21.022800099999998,
                    "lon": 105.68817879999999
                },
                {
                    "lat": 11.5448729,
                    "lon": 104.8921668
                },
                {
                    "lat": 6.2476598,
                    "lon": -75.565816
                },
                {
                    "lat": 38.3591693,
                    "lon": 27.2676116
                },
                {
                    "lat": 43.5671153,
                    "lon": 10.980700299999999
                },
                {
                    "lat": 10.7581899,
                    "lon": 103.82162609999999
                },
                {
                    "lat": 33.3152618,
                    "lon": 44.366065299999995
                },
                {
                    "lat": 27.023803599999997,
                    "lon": 74.21793260000001
                },
                {
                    "lat": -7.5360639,
                    "lon": 112.2384017
                },
                {
                    "lat": 37.3979297,
                    "lon": 14.658782100000002
                },
                {
                    "lat": -6.180495,
                    "lon": 106.8283415
                },
                {
                    "lat": 21.022800099999998,
                    "lon": 105.68817879999999
                },
                {
                    "lat": 0.2540775,
                    "lon": 31.992807799999994
                },
                {
                    "lat": 23.9535742,
                    "lon": 90.14949879999999
                },
                {
                    "lat": 6.2476598,
                    "lon": -75.565816
                },
                {
                    "lat": 24.704981099999998,
                    "lon": 91.67606909999999
                },
                {
                    "lat": 58.8231929,
                    "lon": 56.5872481
                },
                {
                    "lat": -8.8137173,
                    "lon": -36.954107
                },
                {
                    "lat": 23.1793157,
                    "lon": 91.9881527
                },
                {
                    "lat": 29.0587757,
                    "lon": 76.085601
                },
                {
                    "lat": -8.8137173,
                    "lon": -36.954107
                },
                {
                    "lat": 27.6624958,
                    "lon": 85.4375574
                },
                {
                    "lat": -11.4098737,
                    "lon": -41.2808577
                },
                {
                    "lat": -17.930177999999998,
                    "lon": -43.7908453
                },
                {
                    "lat": -6.180495,
                    "lon": 106.8283415
                },
                {
                    "lat": -19.1834229,
                    "lon": -40.3088626
                },
                {
                    "lat": 11.5448729,
                    "lon": 104.8921668
                },
                {
                    "lat": 27.570588599999997,
                    "lon": 80.0981869
                },
                {
                    "lat": -21.2922457,
                    "lon": -50.342843099999996
                },
                {
                    "lat": 35.8898715,
                    "lon": 50.8456323
                },
                {
                    "lat": 27.6624958,
                    "lon": 85.4375574
                },
                {
                    "lat": 14.609053699999997,
                    "lon": 121.02225650000001
                },
                {
                    "lat": 4.6298411,
                    "lon": 11.7068294
                },
                {
                    "lat": 40.1794197,
                    "lon": 44.5408414
                },
                {
                    "lat": 27.023803599999997,
                    "lon": 74.21793260000001
                },
                {
                    "lat": 23.9535742,
                    "lon": 90.14949879999999
                },
                {
                    "lat": 23.9535742,
                    "lon": 90.14949879999999
                },
                {
                    "lat": 53.465789099999995,
                    "lon": 15.182258099999999
                },
                {
                    "lat": 42.7570109,
                    "lon": 23.4504683
                },
                {
                    "lat": -7.150975,
                    "lon": 110.14025939999999
                },
                {
                    "lat": 15.912899800000002,
                    "lon": 79.7399875
                },
                {
                    "lat": -6.9042365000000006,
                    "lon": 39.1959729
                },
                {
                    "lat": -1.3106691,
                    "lon": 36.825027399999996
                },
                {
                    "lat": 11.5448729,
                    "lon": 104.8921668
                },
                {
                    "lat": 39.904520399999996,
                    "lon": 116.4072503
                },
                {
                    "lat": 27.023803599999997,
                    "lon": 74.21793260000001
                },
                {
                    "lat": -26.785177299999997,
                    "lon": 31.810707899999997
                },
                {
                    "lat": 4.6298411,
                    "lon": 11.7068294
                },
                {
                    "lat": 27.570588599999997,
                    "lon": 80.0981869
                },
                {
                    "lat": 23.1793157,
                    "lon": 91.9881527
                },
                {
                    "lat": 11.127122499999999,
                    "lon": 78.6568942
                },
                {
                    "lat": 6.2476598,
                    "lon": -75.565816
                }
            ],
            "region_name": [
                "Punjab",
                "Littoral Region",
                "Jakarta",
                "Tehran Province",
                "San Juan",
                "State of Minas Gerais",
                "Maharashtra",
                "Phnom Penh",
                "Cear\u00e1",
                "Beijing",
                "Shanghai",
                "Ha Noi",
                "Antioquia",
                "\u0130zmir",
                "Tuscany",
                "Sihanoukville",
                "Baghdad Governorate",
                "Rajasthan",
                "East Java",
                "Sicily",
                "Central Region",
                "Dhaka Division",
                "Sylhet Division",
                "Perm Krai",
                "State of Pernambuco",
                "Chittagong Division",
                "Haryana",
                "Bagmati Province",
                "State of Bahia",
                "State of Esp\u00edrito Santo",
                "Uttar Pradesh",
                "State of S\u00e3o Paulo",
                "Alborz Province",
                "Metro Manila",
                "Centre Region",
                "Yerevan",
                "West Pomeranian Voivodeship",
                "Sofia City Province",
                "Central Java",
                "Andhra Pradesh",
                "Dar es Salam",
                "Nairobi County",
                "Lubombo Region",
                "Tamil Nadu"
            ]
        },
        "ip": [
            "81.2.69.192",
            "1.128.0.0",
            "81.2.69.142",
            "216.160.83.56",
            "81.2.69.144",
            "175.16.199.0"
        ]
    },
    "message": "Spike detected in malicious IPs attempting to exploit CVE-2017-20149.",
    "related": {
        "ip": [
            "175.16.199.0",
            "216.160.83.56",
            "81.2.69.142",
            "81.2.69.144",
            "81.2.69.192",
            "1.128.0.0"
        ]
    },
    "rule": {
        "description": "Detects events where source or principal IP matches a malicious or suspicious IP in GreyNoise intelligence.",
        "id": "ru_83168d14-6ada-4f44-b802-b531de16a80b",
        "name": "greynoise_intelligence_ip_match",
        "version": "ru_83168d14-6ada-4f44-b802-b531de16a80b@v_1765958150_356360000"
    },
    "data_stream": {
        "dataset": "google_secops.alert_v2",
        "namespace": "default",
        "type": "logs"
    },
    "input": {
        "type": "cel"
    },
    "tags": [
        "forwarded",
        "google_secops-alert-v2"
    ]
}
```

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Date/time when the event originated. This is the date/time extracted from the event, typically representing when the event was generated by the source. If the event source has no original timestamp, this value is typically populated by the first time the event was received by the pipeline. Required field for all events. | date |
| data_stream.dataset | The field can contain anything that makes sense to signify the source of the data. Examples include `nginx.access`, `prometheus`, `endpoint` etc. For data streams that otherwise fit, but that do not have dataset set we use the value "generic" for the dataset value. `event.dataset` should have the same value as `data_stream.dataset`. Beyond the Elasticsearch data stream naming criteria noted above, the `dataset` value has additional restrictions:   \* Must not contain `-`   \* No longer than 100 characters | constant_keyword |
| data_stream.namespace | A user defined namespace. Namespaces are useful to allow grouping of data. Many users already organize their indices this way, and the data stream naming scheme now provides this best practice as a default. Many users will populate this field with `default`. If no value is used, it falls back to `default`. Beyond the Elasticsearch index naming criteria noted above, `namespace` value has the additional restrictions:   \* Must not contain `-`   \* No longer than 100 characters | constant_keyword |
| data_stream.type | An overarching type for the data stream. Currently allowed values are "logs" and "metrics". We expect to also add "traces" and "synthetics" in the near future. | constant_keyword |
| event.dataset | Name of the dataset. If an event source publishes more than one type of log or events (e.g. access log, error log), the dataset is used to specify which one the event comes from. It's recommended but not required to start the dataset name with the module name, followed by a dot, then the dataset name. | constant_keyword |
| event.module | Name of the module this data is coming from. If your monitoring agent supports the concept of modules or plugins to process events of a given source (e.g. Apache logs), `event.module` should contain the name of this module. | constant_keyword |
| google_secops.alert_v2.created_time | Time the detection was created. | date |
| google_secops.alert_v2.detection.alert_state | Indicates whether the rule generating this detection currently has alerting enabled or disabled. | keyword |
| google_secops.alert_v2.detection.description | Description of the rule that generated the detection. This description is copied from the rule metadata's `description` key, if it is set. | keyword |
| google_secops.alert_v2.detection.detection_fields | Fields specified in the rule for "MULTI_EVENT" rules, keyed by field name. | flattened |
| google_secops.alert_v2.detection.outcomes | Outcome variables from the detection rule, keyed by outcome name. | flattened |
| google_secops.alert_v2.detection.risk_score |  | long |
| google_secops.alert_v2.detection.rule_id | Identifier for the rule generating the detection. | keyword |
| google_secops.alert_v2.detection.rule_labels | Rule metadata labels from the detection, keyed by label name. | flattened |
| google_secops.alert_v2.detection.rule_name | Name of the rule generating the detection, as parsed from `ruleText`. | keyword |
| google_secops.alert_v2.detection.rule_type | Whether the rule generating this detection is a single event or multi-event rule ("SINGLE_EVENT" or "MULTI_EVENT"). | keyword |
| google_secops.alert_v2.detection.rule_version | Identifier for the rule version generating the detection. | keyword |
| google_secops.alert_v2.detection.url_back_to_product | URL pointing to the Google Security Operations application page for this detection. | keyword |
| google_secops.alert_v2.detection.variables.risk_score.int64_val |  | long |
| google_secops.alert_v2.detection.variables.risk_score.type |  | keyword |
| google_secops.alert_v2.detection.variables.risk_score.value |  | long |
| google_secops.alert_v2.detection_time | String representing the time period the detection was found in. | date |
| google_secops.alert_v2.event.about.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.about.group.group_display_name | Group display name. e.g. "Finance". | keyword |
| google_secops.alert_v2.event.about.labels.key | The key. | keyword |
| google_secops.alert_v2.event.about.labels.value | The value. | keyword |
| google_secops.alert_v2.event.about.url | The URL. | keyword |
| google_secops.alert_v2.event.about.user.group_identifiers | Product object identifiers of the group(s) the user belongs to A vendor-specific identifier to uniquely identify the group(s) the user belongs to (a GUID, LDAP OID, or similar). | keyword |
| google_secops.alert_v2.event.about.user.user_display_name | The display name of the user (e.g. "John Locke"). | keyword |
| google_secops.alert_v2.event.about.user.userid | The ID of the user. | keyword |
| google_secops.alert_v2.event.additional |  | flattened |
| google_secops.alert_v2.event.extracted |  | flattened |
| google_secops.alert_v2.event.intermediary.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert_v2.event.metadata.base_labels.allow_scoped_access |  | boolean |
| google_secops.alert_v2.event.metadata.base_labels.ingestion_kv_labels.key |  | keyword |
| google_secops.alert_v2.event.metadata.base_labels.ingestion_kv_labels.value |  | keyword |
| google_secops.alert_v2.event.metadata.base_labels.log_types |  | keyword |
| google_secops.alert_v2.event.metadata.description | A human-readable unparsable description of the event. | keyword |
| google_secops.alert_v2.event.metadata.enrichment_labels.allow_scoped_access |  | boolean |
| google_secops.alert_v2.event.metadata.enrichment_labels.ingestion_kv_labels.key |  | keyword |
| google_secops.alert_v2.event.metadata.enrichment_labels.ingestion_kv_labels.value |  | keyword |
| google_secops.alert_v2.event.metadata.enrichment_labels.log_types |  | keyword |
| google_secops.alert_v2.event.metadata.event_timestamp | The GMT timestamp when the event was generated. | date |
| google_secops.alert_v2.event.metadata.event_type | The event type. If an event has multiple possible types, this specifies the most specific type. | keyword |
| google_secops.alert_v2.event.metadata.id | ID of the UDM event. Can be used for raw and normalized event retrieval. | keyword |
| google_secops.alert_v2.event.metadata.ingested_timestamp | The GMT timestamp when the event was ingested (received) by Google Security Operations. | date |
| google_secops.alert_v2.event.metadata.ingestion_labels.key |  | keyword |
| google_secops.alert_v2.event.metadata.ingestion_labels.value |  | keyword |
| google_secops.alert_v2.event.metadata.log_type | The string value of log type. | keyword |
| google_secops.alert_v2.event.metadata.product_deployment_id | The deployment identifier assigned by the vendor for a product deployment. | keyword |
| google_secops.alert_v2.event.metadata.product_event_type | A short, descriptive, human-readable, product-specific event name or type (for example: "Scanned X", "User account created", "process_start"). | keyword |
| google_secops.alert_v2.event.metadata.product_log_id | A vendor-specific event identifier to uniquely identify the event (for example: a GUID). | keyword |
| google_secops.alert_v2.event.metadata.product_name | The name of the product. | keyword |
| google_secops.alert_v2.event.metadata.url_back_to_product | A URL that takes the user to the source product console for this event. | keyword |
| google_secops.alert_v2.event.metadata.vendor_name | The name of the product vendor. | keyword |
| google_secops.alert_v2.event.network.application_protocol | The application protocol. | keyword |
| google_secops.alert_v2.event.network.dns.answers.data | The payload or response to the DNS question for all responses encoded in UTF-8 format. | keyword |
| google_secops.alert_v2.event.network.dns.answers.name | The name of the owner of the resource record. | keyword |
| google_secops.alert_v2.event.network.dns.answers.type | The code specifying the type of the resource record. | keyword |
| google_secops.alert_v2.event.network.dns.questions.name | The domain name. | keyword |
| google_secops.alert_v2.event.network.dns.questions.type | The code specifying the type of the query. | keyword |
| google_secops.alert_v2.event.network.dns_domain | DNS domain name. | keyword |
| google_secops.alert_v2.event.network.email.bcc | A list of 'bcc' addresses. | keyword |
| google_secops.alert_v2.event.network.email.cc | A list of 'cc' addresses. | keyword |
| google_secops.alert_v2.event.network.email.from | The 'from' address. | keyword |
| google_secops.alert_v2.event.network.email.reply_to | The 'reply to' address. | keyword |
| google_secops.alert_v2.event.network.email.subject | The subject line(s) of the email. | keyword |
| google_secops.alert_v2.event.network.email.to | A list of 'to' addresses. | keyword |
| google_secops.alert_v2.event.network.ftp.command | The FTP command. | keyword |
| google_secops.alert_v2.event.network.http.method | The HTTP request method (e.g. "GET", "POST", "PATCH", "DELETE"). | keyword |
| google_secops.alert_v2.event.network.http.referral_url | The URL for the HTTP referer. | keyword |
| google_secops.alert_v2.event.network.http.response_code | The response status code, for example 200, 302, 404, or 500. | long |
| google_secops.alert_v2.event.network.http.user_agent | The User-Agent request header which includes the application type, operating system, software vendor or software version of the requesting software user agent. | keyword |
| google_secops.alert_v2.event.network.ip_protocol | The IP protocol. | keyword |
| google_secops.alert_v2.event.network.session_id | The ID of the network session. | keyword |
| google_secops.alert_v2.event.principal.asset.asset_id | The asset ID. | keyword |
| google_secops.alert_v2.event.principal.asset.attribute.labels.key | The key. | keyword |
| google_secops.alert_v2.event.principal.asset.attribute.labels.value | The value. | keyword |
| google_secops.alert_v2.event.principal.asset.hardware.serial_number | Hardware serial number. | keyword |
| google_secops.alert_v2.event.principal.asset.hostname | Asset hostname or domain name field. | keyword |
| google_secops.alert_v2.event.principal.asset.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert_v2.event.principal.asset.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert_v2.event.principal.asset.platform_software.platform | The platform operating system. | keyword |
| google_secops.alert_v2.event.principal.asset.platform_software.platform_patch_level | The platform software patch level ( e.g. "Build 17134.48", "SP1"). | keyword |
| google_secops.alert_v2.event.principal.asset.platform_software.platform_version | The platform software version ( e.g. "Microsoft Windows 1803"). | keyword |
| google_secops.alert_v2.event.principal.asset.product_object_id | A vendor-specific identifier to uniquely identify the entity (a GUID or similar). | keyword |
| google_secops.alert_v2.event.principal.asset.software.version | The version of the software. | keyword |
| google_secops.alert_v2.event.principal.asset.type | The type of the asset (e.g. workstation or laptop or server). | keyword |
| google_secops.alert_v2.event.principal.asset_id | The asset ID. | keyword |
| google_secops.alert_v2.event.principal.cloud.environment | The Cloud environment. | keyword |
| google_secops.alert_v2.event.principal.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.group.group_display_name | Group display name. e.g. "Finance". | keyword |
| google_secops.alert_v2.event.principal.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert_v2.event.principal.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert_v2.event.principal.ip_geo_artifact.ip | IP address of the artifact. | ip |
| google_secops.alert_v2.event.principal.ip_geo_artifact.location.country_or_region | The country or region. | keyword |
| google_secops.alert_v2.event.principal.ip_geo_artifact.location.region_coordinates.lat | The latitude in degrees. . | double |
| google_secops.alert_v2.event.principal.ip_geo_artifact.location.region_coordinates.lon | The longitude in degrees. . | double |
| google_secops.alert_v2.event.principal.ip_geo_artifact.location.state | The state. | keyword |
| google_secops.alert_v2.event.principal.ip_geo_artifact.network.asn | Autonomous system number. | keyword |
| google_secops.alert_v2.event.principal.ip_geo_artifact.network.carrier_name | Carrier identification. | keyword |
| google_secops.alert_v2.event.principal.labels.key |  | keyword |
| google_secops.alert_v2.event.principal.labels.value |  | keyword |
| google_secops.alert_v2.event.principal.location.country_or_region | The country or region. | keyword |
| google_secops.alert_v2.event.principal.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert_v2.event.principal.platform | Platform. | keyword |
| google_secops.alert_v2.event.principal.port | Source or destination network port number when a specific network connection is described within an event. | long |
| google_secops.alert_v2.event.principal.process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.principal.process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.process.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.file.names | Names fields. | keyword |
| google_secops.alert_v2.event.principal.process.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.file.signature_info.sigcheck.signers.name | Common name of the signers/certificate. The order of the signers matters. Each element is a higher level authority, the last being the root authority. | keyword |
| google_secops.alert_v2.event.principal.process.file.signature_info.sigcheck.verification_message | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.file.names | Names fields. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.file.signature_info.sigcheck.signers.name | Common name of the signers/certificate. The order of the signers matters. Each element is a higher level authority, the last being the root authority. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.file.signature_info.sigcheck.verification_message | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.file.names | Names fields. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.file.signature_info.sigcheck.verification_message | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.file.names | Names fields. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.file.signature_info.sigcheck.verification_message | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.file.names | Names fields. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.file.signature_info.sigcheck.verification_message | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.pid | The process ID. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.parent_process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.pid | The process ID. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.parent_process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.pid | The process ID. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.parent_process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.principal.process.parent_process.pid | The process ID. | long |
| google_secops.alert_v2.event.principal.process.parent_process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.principal.process.pid | The process ID. | long |
| google_secops.alert_v2.event.principal.process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.file.names | Names fields. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.file.signature_info.sigcheck.verification_message | Status of the certificate. Valid values are "Signed", "Unsigned" or a description of the certificate anomaly, if found. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.pid | The process ID. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.parent_process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.principal.process_ancestors.pid | The process ID. | keyword |
| google_secops.alert_v2.event.principal.registry.registry_key | Registry key associated with an application or system component (e.g., HKEY_, HKCU\Environment...). | keyword |
| google_secops.alert_v2.event.principal.registry.registry_value_name | Name of the registry value associated with an application or system component (e.g. TEMP). | keyword |
| google_secops.alert_v2.event.principal.resource.attribute.cloud.project.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert_v2.event.principal.resource.attribute.cloud.project.resource_subtype | Resource sub-type (e.g. "BigQuery", "Bigtable"). | keyword |
| google_secops.alert_v2.event.principal.resource.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert_v2.event.principal.url | The URL. | keyword |
| google_secops.alert_v2.event.principal.user.attribute.labels.key |  | keyword |
| google_secops.alert_v2.event.principal.user.attribute.labels.value |  | keyword |
| google_secops.alert_v2.event.principal.user.attribute.permissions.name | Name of the permission (e.g. chronicle.analyst.updateRule). | keyword |
| google_secops.alert_v2.event.principal.user.attribute.permissions.type | Type of the permission. | keyword |
| google_secops.alert_v2.event.principal.user.attribute.roles.description | System role description for user. | keyword |
| google_secops.alert_v2.event.principal.user.attribute.roles.name | System role name for user. | keyword |
| google_secops.alert_v2.event.principal.user.email_addresses | Email addresses of the user. | keyword |
| google_secops.alert_v2.event.principal.user.group_identifiers | Product object identifiers of the group(s) the user belongs to A vendor-specific identifier to uniquely identify the group(s) the user belongs to (a GUID, LDAP OID, or similar). | keyword |
| google_secops.alert_v2.event.principal.user.product_object_id | A vendor-specific identifier to uniquely identify the entity (e.g. a GUID, LDAP, OID, or similar). | keyword |
| google_secops.alert_v2.event.principal.user.user_display_name | The display name of the user (e.g. "John Locke"). | keyword |
| google_secops.alert_v2.event.principal.user.userid | The ID of the user. | keyword |
| google_secops.alert_v2.event.principal.user.windows_sid | The Microsoft Windows SID of the user. | keyword |
| google_secops.alert_v2.event.security_result.about.resource.name |  | keyword |
| google_secops.alert_v2.event.security_result.about.user.attribute.roles.name | System role name for user. | keyword |
| google_secops.alert_v2.event.security_result.about.user.email_addresses | Email addresses of the user. | keyword |
| google_secops.alert_v2.event.security_result.action | Actions taken for this event. | keyword |
| google_secops.alert_v2.event.security_result.alert_state | The alerting types of this security result. | keyword |
| google_secops.alert_v2.event.security_result.category | The security category. | keyword |
| google_secops.alert_v2.event.security_result.category_details | For vendor-specific categories. For web categorization, put type in here such as "gambling" or "porn". | keyword |
| google_secops.alert_v2.event.security_result.description | A human readable description (e.g. "user password was wrong").' | keyword |
| google_secops.alert_v2.event.security_result.detection_fields.key |  | keyword |
| google_secops.alert_v2.event.security_result.detection_fields.value |  | keyword |
| google_secops.alert_v2.event.security_result.first_discovered_time | First time the IoC threat was discovered in the provider. | date |
| google_secops.alert_v2.event.security_result.priority | The priority of the result. | keyword |
| google_secops.alert_v2.event.security_result.priority_details | Vendor-specific information about the security result priority. | keyword |
| google_secops.alert_v2.event.security_result.rule_id | A vendor-specific ID and name for a rule, varying by observerer type (e.g. "08123", "5d2b44d0-5ef6-40f5-a704-47d61d3babbe"). | keyword |
| google_secops.alert_v2.event.security_result.rule_labels.key |  | keyword |
| google_secops.alert_v2.event.security_result.rule_labels.value |  | keyword |
| google_secops.alert_v2.event.security_result.rule_name | Name of the security rule (e.g. "BlockInboundToOracle"). | keyword |
| google_secops.alert_v2.event.security_result.rule_type | The type of security rule. | keyword |
| google_secops.alert_v2.event.security_result.severity | The severity of the result. | keyword |
| google_secops.alert_v2.event.security_result.severity_details | Vendor-specific severity. | keyword |
| google_secops.alert_v2.event.security_result.summary | A human readable summary (e.g. "failed login occurred"). | keyword |
| google_secops.alert_v2.event.security_result.threat_id | Vendor-specific ID for a threat. | keyword |
| google_secops.alert_v2.event.security_result.threat_id_namespace | The attribute threat_id_namespace qualifies threat_id with an ID namespace to get an unique ID. The attribute threat_id by itself is not unique across Google SecOps as it is a vendor specific ID. | keyword |
| google_secops.alert_v2.event.security_result.threat_name | A vendor-assigned classification common across multiple customers (e.g. "W32/File-A", "Slammer"). | keyword |
| google_secops.alert_v2.event.security_result.url_back_to_product | URL that takes the user to the source product console for this event. | keyword |
| google_secops.alert_v2.event.src.asset.asset_id | The asset ID. | keyword |
| google_secops.alert_v2.event.src.asset.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert_v2.event.src.asset.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert_v2.event.src.asset.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert_v2.event.src.asset_id | The asset ID. | keyword |
| google_secops.alert_v2.event.src.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.src.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.src.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.src.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert_v2.event.src.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert_v2.event.src.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert_v2.event.src.process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.src.user.email_addresses | Email addresses of the user. | keyword |
| google_secops.alert_v2.event.src.user.product_object_id | A vendor-specific identifier to uniquely identify the entity (e.g. a GUID, LDAP, OID, or similar). | keyword |
| google_secops.alert_v2.event.src.user.userid | The ID of the user. | keyword |
| google_secops.alert_v2.event.src.user.windows_sid | The Microsoft Windows SID of the user. | keyword |
| google_secops.alert_v2.event.target.application | The name of an application or service. Some SSO solutions only capture the name of a target application such as "Atlassian" or "Google". | keyword |
| google_secops.alert_v2.event.target.asset.asset_id | The asset ID. Value must contain the ':' character. For example, cs:abcdd23434. | keyword |
| google_secops.alert_v2.event.target.asset.hostname | Asset hostname or domain name field. | keyword |
| google_secops.alert_v2.event.target.asset.ip | A list of IP addresses associated with an asset. | ip |
| google_secops.alert_v2.event.target.asset.mac | List of MAC addresses associated with an asset. | keyword |
| google_secops.alert_v2.event.target.asset_id | The asset ID. | keyword |
| google_secops.alert_v2.event.target.cloud.environment | The Cloud environment. | keyword |
| google_secops.alert_v2.event.target.cloud.project.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert_v2.event.target.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.target.file.last_modification_time | Timestamp when the file was last updated. | date |
| google_secops.alert_v2.event.target.file.md5 | The MD5 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.target.file.names | Names fields. | keyword |
| google_secops.alert_v2.event.target.file.sha1 | The SHA1 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.target.file.sha256 | The SHA256 hash of the file, as a hex-encoded string. | keyword |
| google_secops.alert_v2.event.target.file.size | The size of the file in bytes. | keyword |
| google_secops.alert_v2.event.target.group.group_display_name | Group display name. e.g. "Finance". | keyword |
| google_secops.alert_v2.event.target.hostname | Client hostname or domain name field. Hostname also doubles as the domain for remote entities. | keyword |
| google_secops.alert_v2.event.target.ip | A list of IP addresses associated with a network connection. | ip |
| google_secops.alert_v2.event.target.labels.key |  | keyword |
| google_secops.alert_v2.event.target.labels.value |  | keyword |
| google_secops.alert_v2.event.target.mac | List of MAC addresses associated with a device. | keyword |
| google_secops.alert_v2.event.target.port | Source or destination network port number when a specific network connection is described within an event. | long |
| google_secops.alert_v2.event.target.process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.target.process.file.first_seen_time | Timestamp the file was first seen in the customer's environment. | date |
| google_secops.alert_v2.event.target.process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.target.process.file.last_modification_time | Timestamp when the file was last updated. | date |
| google_secops.alert_v2.event.target.process.parent_process.command_line | The command line command that created the process. | keyword |
| google_secops.alert_v2.event.target.process.parent_process.file.full_path | The full path identifying the location of the file on the system. | keyword |
| google_secops.alert_v2.event.target.process.pid | The process ID. | keyword |
| google_secops.alert_v2.event.target.process.product_specific_process_id | A product specific id for the parent process. Please use parentProcess.productSpecificProcessId instead. | keyword |
| google_secops.alert_v2.event.target.registry.registry_key | Registry key associated with an application or system component (e.g., HKEY_, HKCU\Environment...). | keyword |
| google_secops.alert_v2.event.target.registry.registry_value_data | Data associated with a registry value (e.g. %USERPROFILE%\Local Settings\Temp). | keyword |
| google_secops.alert_v2.event.target.registry.registry_value_name | Name of the registry value associated with an application or system component (e.g. TEMP). | keyword |
| google_secops.alert_v2.event.target.resource.attribute.labels.key |  | keyword |
| google_secops.alert_v2.event.target.resource.attribute.labels.value |  | keyword |
| google_secops.alert_v2.event.target.resource.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert_v2.event.target.resource.product_object_id | A vendor-specific identifier to uniquely identify the entity (a GUID, OID, or similar). | keyword |
| google_secops.alert_v2.event.target.resource.resource_type | Resource type. | keyword |
| google_secops.alert_v2.event.target.resource_ancestors.name | The full name of the resource. For example, Google Cloud: //cloudresourcemanager.googleapis.com/projects/wombat-123, and AWS: arn:aws:iam::123456789012:user/johndoe. | keyword |
| google_secops.alert_v2.event.target.resource_ancestors.product_object_id | A vendor-specific identifier to uniquely identify the entity (a GUID, OID, or similar). | keyword |
| google_secops.alert_v2.event.target.user.email_addresses | Email addresses of the user. | keyword |
| google_secops.alert_v2.event.target.user.group_identifiers | Product object identifiers of the group(s) the user belongs to A vendor-specific identifier to uniquely identify the group(s) the user belongs to (a GUID, LDAP OID, or similar). | keyword |
| google_secops.alert_v2.event.target.user.product_object_id | A vendor-specific identifier to uniquely identify the entity (e.g. a GUID, LDAP, OID, or similar). | keyword |
| google_secops.alert_v2.event.target.user.user_display_name | The display name of the user (e.g. "John Locke"). | keyword |
| google_secops.alert_v2.event.target.user.userid | The ID of the user. | keyword |
| google_secops.alert_v2.event.target.user.windows_sid | The Microsoft Windows SID of the user. | keyword |
| google_secops.alert_v2.friendly_name | Alert Rule Name. | keyword |
| google_secops.alert_v2.id | Identifier for the detection. Same as "detection_id". | keyword |
| google_secops.alert_v2.label | The variable a given set of UDM events belongs to. | keyword |
| google_secops.alert_v2.time_window.end_time | String representing the end of the time window in which the detection was found, in RFC 3339 format. | date |
| google_secops.alert_v2.time_window.start_time | String representing the start of the time window in which the detection was found, in RFC 3339 format. | date |
| google_secops.alert_v2.type | Type of detection (type is always `RULE_DETECTION`). | keyword |
| input.type | Type of Filebeat input. | keyword |
| tags | User defined tags. | keyword |

