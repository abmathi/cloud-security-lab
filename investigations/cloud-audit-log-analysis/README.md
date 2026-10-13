# Google Cloud Audit Log Analysis — Destructive Activity Investigation

## Executive Summary

This investigation examines administrative activity in Google Cloud using Cloud Audit Logs, Logs Explorer, Log Router, BigQuery, and SQL.

The exercise was based on a simulated security incident involving unauthorized cloud resource access at Cymbal Bank. A controlled Google Cloud lab environment was used to generate administrative actions and analyze the resulting audit events.

The investigation focused on identifying destructive operations involving Compute Engine virtual machines and Cloud Storage buckets.

An independent investigation extended the guided analysis by correlating multiple resource-deletion events into a single administrative activity timeline.

The objective was to demonstrate how cloud audit telemetry can support incident reconstruction, identity attribution, and security investigations.

## Investigation Scope

The investigation was conducted in a temporary, authorized Google Cloud lab environment using test accounts and cloud resources.

| Component | Investigation Purpose |
| --- | --- |
| Cloud Audit Logs | Record administrative activity |
| Logs Explorer | Search and inspect cloud audit events |
| Log Router | Export selected log events |
| BigQuery | Analyze exported audit records |
| SQL | Filter and correlate administrative operations |
| Compute Engine | Investigate VM deletion events |
| Cloud Storage | Investigate bucket deletion events |
| IAM principals | Attribute logged operations to authenticated identities |

### Investigation Objectives

1. Generate controlled administrative events involving cloud resources.
2. Examine the resulting Cloud Audit Logs.
3. Configure a Log Router sink for audit-log export.
4. Analyze exported events in BigQuery.
5. Identify Compute Engine virtual machine deletion operations.
6. Identify Cloud Storage bucket deletion operations.
7. Independently correlate destructive operations across cloud resource types.
8. Assess what audit logs establish and what requires additional evidence.

## Evidence Sources and Methodology

### Evidence Sources

The investigation used Google Cloud audit records, log-export configuration, BigQuery SQL results, and saved screenshots from the lab.

The source materials include guided exercise instructions and independent investigation notes.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Preparation | Establish authorized test accounts and cloud resources |
| Activity generation | Perform controlled cloud administrative operations |
| Log analysis | Examine administrative records in Logs Explorer |
| Log export | Configure Log Router and BigQuery |
| SQL investigation | Query VM and bucket deletion events |
| Correlation | Build a cross-service activity timeline |
| Assessment | Interpret the observed activity and document limitations |

### Evidence Standards

The investigation distinguishes recorded administrative operations from assumptions about an attacker's identity, intent, or access method.

A cloud audit entry can identify a logged operation, associated principal, resource, and timestamp. It does not independently establish that the activity was malicious.

The destructive actions in this exercise were intentionally generated within an authorized training environment.

The final analysis will therefore describe the observed administrative actions as simulated incident activity rather than an independently verified real-world compromise.

## Investigation Organization

The report documents five investigation stages:

1. Administrative Activity Generation and Audit Log Inspection
2. Audit Log Export and BigQuery Configuration
3. Compute Engine Deletion Analysis
4. Cloud Storage Deletion Analysis
5. Independent Cross-Service Activity Correlation

The final sections summarize security implications, evidence limitations, skills demonstrated, and supporting evidence.

## Investigation Findings

### Finding 1 — Cloud Storage Deletion Activity in Audit Logs

#### Investigation Context

The first investigation examined Google Cloud Admin Activity logs to identify administrative operations involving Cloud Storage resources.

The scenario involved suspected unauthorized activity within Cymbal Bank's cloud environment. To reproduce relevant evidence, administrative operations were performed in an authorized training project.

The objective was to isolate bucket-deletion events, identify the affected cloud service, and determine which details were available for further investigation.

#### Log Query

I used Google Cloud Logs Explorer to filter Admin Activity records for Cloud Storage deletion operations.

The query was:

```text

logName = "projects/qwiklabs-gcp-01-f0ab12281570/logs/cloudaudit.googleapis.com%2Factivity"

protoPayload.serviceName="storage.googleapis.com"

protoPayload.methodName="storage.buckets.delete"

```

The filters targeted:

| Field | Purpose |
| --- | --- |
| `logName` | Select Admin Activity audit records |
| `protoPayload.serviceName` | Restrict results to Cloud Storage |
| `protoPayload.methodName` | Identify bucket-deletion operations |

#### Observed Audit Events

The filtered query returned three matching entries.

| Event | Timestamp (MST) | Operation |
| --- | --- | --- |
| 1 | 15:10:46 | `storage.buckets.delete` |
| 2 | 15:27:15 | `storage.buckets.delete` |
| 3 | 15:27:19 | `storage.buckets.delete` |

![Google Cloud Storage deletion audit logs](evidence/01-cloud-storage-deletion-logs.png)

**Figure 1 — Google Cloud Logs Explorer displaying three Admin Activity records filtered by `storage.googleapis.com` and `storage.buckets.delete`. The results identify bucket-deletion operations and their timestamps.**

All three records were associated with Cloud Storage bucket-deletion activity.

The results included event summaries and principal-identification fields. However, the screenshot did not expand the individual records sufficiently to establish every affected resource or fully verify the principal associated with each event.

The visible records supported identification of the deletion method and event sequence, but further field inspection would be required for a complete attribution assessment.

#### Security Relevance

Cloud Storage bucket deletion is a destructive administrative operation that can affect resource availability and data retention.

In a security investigation, such events may warrant review of the acting principal, affected resource, authorization context, and related administrative activity.

However, the presence of bucket-deletion records does not independently establish malicious intent or unauthorized access.

In this exercise, the operations were generated in an authorized test environment to reproduce a simulated incident.

#### Analyst Assessment

****Assessment: Three Cloud Storage bucket-deletion audit records identified.****

Logs Explorer successfully isolated administrative activity associated with the `storage.buckets.delete` method.

The events provided a starting point for investigating destructive cloud-resource activity and demonstrated the value of filtering audit records by service and operation.

The next stages will examine log export, BigQuery analysis, and correlation across multiple cloud resource types.

### Finding 2 — Audit Event Attribution and Log Export Configuration

#### Investigation Context

After identifying Cloud Storage bucket-deletion events, I examined the structure of an individual Google Cloud Admin Activity audit record.

The objective was to identify the fields available for reconstructing administrative activity and prepare the logs for further analysis in BigQuery.

#### Audit Record Inspection

Using Logs Explorer, I expanded a `storage.buckets.delete` audit event and examined its structured payload.

The selected record was timestamped September 21, 2026, at approximately 15:10:46 MST.

| Audit Field | Investigation Value |
| --- | --- |
| `protoPayload.methodName` | Identifies the administrative operation |
| `protoPayload.serviceName` | Identifies the Google Cloud service |
| `protoPayload.authenticationInfo` | Contains information about the authenticated principal |
| `protoPayload.authorizationInfo` | Contains authorization-related information |
| `protoPayload.requestMetadata` | May provide additional request context |
| `protoPayload.resourceName` | Identifies the resource associated with the event |
| `timestamp` | Supports chronological investigation |
| `receiveTimestamp` | Records when Cloud Logging received the event |

![Expanded Google Cloud audit event](evidence/02-expanded-audit-event.png)

**Figure 2 — Expanded Google Cloud Admin Activity record for a Cloud Storage bucket-deletion operation. The structured payload exposes service, method, resource, timestamp, and attribution-related fields.**

The expanded event identified `storage.googleapis.com` as the service and `storage.buckets.delete` as the administrative method.

The record also included a Cloud Storage bucket resource name and principal information in the event summary.

Although the audit payload exposed additional structured fields, several nested objects remained collapsed in the preserved screenshot. Their complete contents were not independently verified from that image.

#### Log Export Configuration

The next stage of the exercise configured Google Cloud Log Router to send selected audit logs to BigQuery.

Following the lab procedure, I created a log sink with the following configuration:

| Component | Configuration |
| --- | --- |
| Sink name | `AuditLogsExport` |
| Destination type | BigQuery dataset |
| Dataset | `auditlogs_dataset` |
| Intended log scope (lab instructions) | Cloud Audit Logs Admin Activity |

The enabled sink was configured to route matching future log entries to BigQuery for SQL-based analysis. The sink listing alone does not verify its filter or end-to-end delivery; the later BigQuery results provide evidence that audit records were available for analysis.

![Enabled Google Cloud Log Router BigQuery sink](evidence/06-log-router-bigquery-sink.png)

*Figure 6 — Supplemental Log Router evidence showing the enabled `AuditLogsExport` sink with a BigQuery dataset destination, `auditlogs_dataset`. The visible volume is 0 B at capture time; the screenshot confirms sink configuration, not successful event delivery.*

An important operational consideration is that creating a Log Router sink does not automatically backfill earlier log records.

Consequently, additional administrative activity was generated after sink creation so the corresponding records could be investigated in BigQuery.

Figure 2 preserves the expanded audit event. A separate supplemental screenshot (Figure 6) confirms that the `AuditLogsExport` sink was enabled and targeted the BigQuery dataset; it does not, by itself, establish successful delivery of each event.

#### Security Relevance

Cloud Audit Logs provide valuable information for investigating destructive administrative actions.

The combination of operation names, affected resources, principal information, and timestamps can support investigation timelines and help analysts identify accounts requiring further review.

Exporting logs to BigQuery enables more flexible SQL-based filtering and correlation across larger sets of audit records.

However, a logged principal identity does not independently establish who physically performed an action or whether the operation was malicious.

#### Analyst Assessment

****Assessment: Structured audit-event fields identified and an export workflow established for subsequent BigQuery analysis.****

The expanded Cloud Storage deletion record exposed relevant administrative metadata, including the service, operation, resource, and attribution-related fields.

The exercise then established a Log Router workflow for analyzing subsequent audit activity in BigQuery.

The following findings examine SQL queries and their supporting evidence.

### Finding 3 — Compute Engine Deletion Analysis in BigQuery

#### Investigation Context

The third investigation used Google Cloud BigQuery to analyze exported Admin Activity audit logs for destructive Compute Engine operations.

The objective was to identify virtual machine deletion events, extract relevant attribution fields, and understand how SQL can support the reconstruction of cloud administrative activity.

The exercise generated a controlled Compute Engine deletion as part of the simulated incident.

#### BigQuery SQL Investigation

I used the following SQL query to identify Compute Engine instance-deletion operations in the exported audit-log dataset:

```sql

SELECT

  timestamp,

  resource.labels.instance_id,

  protopayload_auditlog.authenticationInfo.principalEmail,

  protopayload_auditlog.resourceName,

  protopayload_auditlog.methodName

FROM

  `auditlogs_dataset.cloudaudit_googleapis_com_activity_*`

WHERE

  PARSE_DATE('%Y%m%d', _TABLE_SUFFIX) BETWEEN

    DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY)

    AND CURRENT_DATE()

  AND resource.type = "gce_instance"

  AND operation.first IS TRUE

  AND protopayload_auditlog.methodName = "v1.compute.instances.delete"

ORDER BY

  timestamp,

  resource.labels.instance_id

LIMIT 1000;

```

The query selected the following fields:

| Field | Investigation Purpose |
| --- | --- |
| `timestamp` | Establish when the logged operation occurred |
| `resource.labels.instance_id` | Identify the associated Compute Engine instance |
| `authenticationInfo.principalEmail` | Identify the principal recorded in the audit event |
| `resourceName` | Identify the affected cloud resource |
| `methodName` | Confirm the administrative operation |

![Compute Engine deletion investigation query in BigQuery](evidence/03-compute-engine-deletion-query.png)

**Figure 3 — BigQuery SQL editor showing a query for Compute Engine instance-deletion events using `gce_instance`, `operation.first`, and `v1.compute.instances.delete` filters. The visible results pane contains Cloud Storage deletion records from a different query and does not establish the outcome of the displayed Compute Engine query.**

The query restricted results to Compute Engine instance resources and the `v1.compute.instances.delete` method.

The wildcard-table date filter scoped the search to the seven days preceding the original lab execution. Re-running this historical query now would require adjusting its date window.

#### Evidence Interpretation

The preserved screenshot shows the Compute Engine deletion query in BigQuery's SQL editor.

The query results pane, however, displays two Cloud Storage bucket-deletion records associated with an earlier query.

Therefore, the screenshot supports the construction and preparation of the Compute Engine investigation query but does not independently establish its returned row count, affected instance, or executing principal.

The original lab instructions describe a controlled virtual machine deletion and an expected matching audit record. Those instructions provide scenario context rather than independent verification of the query's results.

#### Security Relevance

Compute Engine instance deletion is a destructive administrative operation that can affect service availability and potentially remove associated resources.

In a cloud security investigation, analysts can use audit records to examine the deletion timestamp, affected instance, recorded principal, and surrounding administrative operations.

Correlating this information with IAM changes, authentication records, and other cloud activity can help determine whether a deletion was authorized or suspicious.

However, a deletion event alone does not establish malicious intent.

#### Analyst Assessment

****Assessment: Compute Engine deletion investigation query constructed using exported Cloud Audit Logs.****

The investigation demonstrated SQL filtering of audit records by resource type, administrative method, and time range.

The preserved screenshot documents the query but does not independently verify the returned Compute Engine deletion events.

Finding 4 examines Cloud Storage deletion records visible in BigQuery results.

### Finding 4 — Cloud Storage Deletion Analysis in BigQuery

#### Investigation Context

The fourth investigation examined Cloud Storage bucket-deletion activity using audit records exported to Google Cloud BigQuery.

The objective was to identify destructive administrative operations, extract resource and principal information, and establish a timeline of recorded cloud activity.

Unlike the initial Logs Explorer investigation, this stage used SQL to analyze structured audit-log data.

#### BigQuery SQL Investigation

The BigQuery editor displayed a query targeting Cloud Storage bucket-deletion events:

```sql

SELECT

  timestamp,

  resource.labels.bucket_name,

  protopayload_auditlog.authenticationInfo.principalEmail,

  protopayload_auditlog.resourceName,

  protopayload_auditlog.methodName

FROM

  `auditlogs_dataset.cloudaudit_googleapis_com_activity_*`

WHERE

  PARSE_DATE('%Y%m%d', _TABLE_SUFFIX) BETWEEN

    DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY)

    AND CURRENT_DATE()

  AND resource.type = "gcs_bucket"

  AND protopayload_auditlog.methodName = "storage.buckets.delete"

ORDER BY

  timestamp,

  resource.labels.instance_id

LIMIT 1000;

```

The query selected five investigation-relevant fields:

| Field | Investigation Purpose |
| --- | --- |
| `timestamp` | Establish the event time |
| `resource.labels.bucket_name` | Identify the associated Cloud Storage bucket |
| `authenticationInfo.principalEmail` | Identify the principal recorded in the event |
| `resourceName` | Identify the resource referenced by the operation |
| `methodName` | Confirm the administrative method |

The documented query filters Cloud Storage bucket records and the `storage.buckets.delete` method.

The preserved query retains `resource.labels.instance_id` in the `ORDER BY` clause from the earlier Compute Engine investigation. This is not a bucket-specific field. A corrected query would order by `timestamp` and `resource.labels.bucket_name`; the original query is retained here to reflect the saved evidence.

Because the displayed results may belong to a previously executed query, the screenshot does not independently verify execution of the exact SQL currently shown in the editor.

#### Observed Bucket Deletion Records

The BigQuery results pane displayed two Cloud Storage deletion records.

| Event | Timestamp (UTC) | Recorded Principal | Resource Type |
| --- | --- | --- | --- |
| 1 | 2026-09-21 22:27:15.047019 | Training account | Cloud Storage bucket |
| 2 | 2026-09-21 22:27:19.107056 | Training account | Cloud Storage bucket |

![Cloud Storage bucket deletion results in BigQuery](evidence/04-bigquery-bucket-deletion-results.png)

**Figure 4 — BigQuery interface displaying two Cloud Storage bucket-deletion audit records approximately four seconds apart. The results include timestamps, bucket identifiers, resource names, and recorded principal information. The SQL visible in the editor is not independently confirmed as the executed query that produced these rows.**

Both results contained bucket-name and resource-name fields, along with the same visible training-account identity.

The displayed events were separated by approximately four seconds.

The results demonstrated how exported audit logs can support chronological analysis of administrative operations.

However, the preserved screenshot does not expose all record details needed to establish the exact outcome, authorization context, or purpose of each operation.

#### Comparison with Logs Explorer

The initial Logs Explorer investigation identified three matching bucket-deletion audit records.

The BigQuery results displayed two records associated with subsequent administrative activity.

These observations should not be treated as contradictory because the two interfaces may represent different collection windows or sets of exported events.

Google Cloud Log Router sinks do not automatically backfill historical log entries. Consequently, the available BigQuery dataset may not contain every event previously visible in Logs Explorer.

#### Security Relevance

Cloud Storage bucket-deletion records are relevant to investigations involving data availability, destructive activity, and unauthorized administrative changes.

The timestamp, recorded principal, affected resource, and operation name provide a foundation for reconstructing administrative activity.

When multiple destructive operations occur close together, analysts may prioritize additional review of the associated identities, access history, and surrounding cloud operations.

However, temporal proximity and a shared recorded principal do not independently demonstrate malicious activity or compromise.

#### Analyst Assessment

****Assessment: Two Cloud Storage bucket-deletion records identified in exported BigQuery audit data.****

The visible results provided timestamps, recorded principal information, and associated Cloud Storage resources.

The investigation demonstrated how BigQuery can support audit-event filtering and timeline reconstruction.

The records provide evidence of logged deletion operations, but additional audit fields and resource-state evidence would be required to establish their complete outcomes and authorization context.

### Finding 5 — Independent Cross-Service Administrative Activity Correlation

#### Investigation Context

After completing the guided Cloud Audit Logs investigations, I independently developed a BigQuery query to correlate destructive administrative operations across multiple Google Cloud services.

The guided exercises examined Compute Engine and Cloud Storage deletion events separately.

The independent investigation addressed a broader security question:

****Did the same recorded identity perform multiple destructive cloud-resource operations within a short period?****

The objective was to reconstruct an administrative activity timeline using exported audit records rather than investigate each cloud service in isolation.

#### Cross-Service BigQuery Query

I created a SQL query that selected a common set of investigation fields and included both Compute Engine and Cloud Storage deletion methods.

```sql

SELECT

  timestamp,

  protopayload_auditlog.authenticationInfo.principalEmail AS actor,

  protopayload_auditlog.methodName AS action,

  protopayload_auditlog.resourceName AS resource

FROM

  `auditlogs_dataset.cloudaudit_googleapis_com_activity_*`

WHERE

  PARSE_DATE('%Y%m%d', _TABLE_SUFFIX) BETWEEN

    DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY)

    AND CURRENT_DATE()

  AND protopayload_auditlog.methodName IN (

    "v1.compute.instances.delete",

    "storage.buckets.delete"

  )

ORDER BY

  timestamp ASC;

```

The query made two important changes from the guided investigations.

First, it removed the restriction to a single resource type, allowing the query to retrieve operations from multiple Google Cloud services.

Second, it used an `IN` clause to select both deletion methods and ordered the results chronologically.

The field aliases `actor`, `action`, and `resource` made the resulting records easier to interpret as an administrative activity timeline.

#### Correlated Activity Timeline

BigQuery returned three matching records from September 21, 2026.

| Timestamp (UTC) | Action | Resource |
| --- | --- | --- |
| `22:26:42.647691` | `v1.compute.instances.delete` | Compute Engine instance `default-us-vm` |
| `22:27:15.047019` | `storage.buckets.delete` | Cloud Storage bucket |
| `22:27:19.107056` | `storage.buckets.delete` | Cloud Storage bucket |

![Cross-service administrative activity timeline](evidence/05-cross-service-activity-timeline.png)

**Figure 5 — Independent BigQuery query correlating Compute Engine instance deletion and Cloud Storage bucket deletion operations. The results show three events attributed to the same recorded principal within approximately 37 seconds.**

All three records contained the same authenticated training-account identity in the `actor` field.

The first record identified a Compute Engine instance deletion operation. The subsequent two records identified Cloud Storage bucket deletion operations.

The events occurred within approximately 37 seconds of one another.

#### Cross-Service Correlation

The timeline revealed a sequence of administrative actions that would have been less apparent when examining each service independently.

The combination of a shared recorded principal, short time interval, and destructive operation methods provided a useful basis for further security investigation.

The query demonstrated how different cloud service audit records can be normalized into common fields and analyzed together.

However, the observed timeline does not independently establish that the operations were unauthorized or malicious.

The activity was intentionally generated as part of the training scenario. Additional context, such as access approvals, authentication history, IAM changes, and resource-state verification, would be required to assess the legitimacy and ultimate effects of similar activity in a production environment.

#### Security Relevance

Cross-service audit correlation is useful during investigations involving potential cloud account compromise, unauthorized administrative changes, and destructive resource activity.

An analyst examining multiple deletion operations under one identity could use the timeline to determine which resources require further review and what additional telemetry should be collected.

This technique supports cloud incident reconstruction by reducing dependence on separate service-specific queries.

It does not establish an intrusion or prove successful resource destruction solely from matching operation names.

#### Analyst Assessment

****Assessment: Three destructive administrative operation records independently correlated across Compute Engine and Cloud Storage.****

The investigation identified one VM deletion event and two bucket deletion events associated with the same recorded training-account principal.

The events occurred within approximately 37 seconds and were reconstructed into a chronological BigQuery activity timeline.

The result demonstrated practical cloud audit-log correlation and SQL-based incident investigation without assuming malicious intent or independently verified resource-deletion outcomes.

## Security Implications

The investigation demonstrated how Google Cloud Audit Logs can support administrative activity monitoring, cloud incident reconstruction, and cross-service event correlation.

| Security Area | Observation | Security Implication |
| --- | --- | --- |
| Administrative activity monitoring | Cloud Storage deletion events appeared in Logs Explorer | Admin Activity logs provide visibility into cloud-resource operations |
| Identity attribution | Audit records contained authenticated-principal information | Recorded identities provide investigative leads, but do not independently establish who physically performed an action |
| Resource identification | Audit payloads contained affected resource names | Analysts can associate logged operations with cloud assets |
| Log export | Log Router was configured to route Admin Activity logs into BigQuery | Centralized analysis supports SQL-based investigation of exported records |
| Compute Engine investigation | SQL filtered for `v1.compute.instances.delete` | Method-based queries can isolate potentially destructive VM operations |
| Cloud Storage investigation | BigQuery displayed two bucket-deletion records | Exported records support timeline analysis and resource-level investigation |
| Cross-service correlation | Three deletion records were associated with the same recorded principal within approximately 37 seconds | Correlation can reveal administrative activity patterns across multiple cloud services |

### Investigation and Detection Opportunities

The techniques demonstrated in this lab can support security monitoring for destructive administrative activity.

Potential detection opportunities include unusual VM or bucket deletion operations, repeated destructive actions within short periods, and administrative events associated with identities that do not normally manage the affected resources.

However, a useful production detection would need additional context to distinguish authorized maintenance or cleanup from suspicious activity.

Relevant contextual evidence could include IAM role changes, authentication activity, change-management records, resource ownership, and expected administrative behavior.

No production detection rule was implemented or validated as part of this investigation.

### Audit Log Retention and Export Considerations

Exporting Cloud Audit Logs to BigQuery provides a way to conduct SQL-based analysis of selected events.

The investigation also demonstrated an important limitation of log-export workflows: Log Router sinks do not automatically backfill historical events when created.

Consequently, the contents of a newly configured export destination may differ from records previously visible in Logs Explorer.

In a production environment, analysts should verify log coverage, sink filters, destination access, retention configuration, and the time periods represented by exported records before concluding that an event did not occur.

## Evidence Limitations

This investigation was conducted within an authorized Google Cloud training environment using controlled administrative operations.

The following limitations apply:

1. ****Simulated incident:**** The scenario involved suspected unauthorized cloud activity, but the investigated operations were generated for training. No real-world compromise was established.
2. ****Screenshot scope:**** The five numbered screenshots preserve selected log queries, audit-event fields, BigQuery results, and correlation output rather than a complete event export.
3. ****Identity attribution:**** Audit records identified a training-account principal. This does not independently establish the physical operator, intent, or authorization status of each action.
4. ****Audit-event completeness:**** Several structured fields were collapsed in the preserved Logs Explorer screenshots. Their full contents were not independently verified.
5. ****Compute Engine query:**** The screenshot documenting the dedicated VM deletion query did not independently display its corresponding results. A matching VM deletion record was subsequently visible in the independent cross-service query.
6. ****Query-result consistency:**** Some screenshots showed SQL text that did not correspond to the visible results pane. The analysis distinguishes displayed query construction from independently visible result records.
7. ****Export coverage:**** Logs Explorer and BigQuery displayed different numbers of bucket-deletion records. The evidence does not independently establish complete log-export coverage or the exact reason for the difference.
8. ****Operation outcome:**** Audit entries documenting deletion methods do not, by themselves, prove the final state of every associated cloud resource.
9. ****Environment scope:**** The investigation used temporary training resources and did not evaluate production cloud monitoring, enterprise access governance, or long-term audit retention.

## Skills Demonstrated

### Google Cloud Security Operations

- Investigated administrative activity using Cloud Audit Logs.
- Applied Logs Explorer filters to isolate Cloud Storage deletion methods.
- Examined structured audit fields for operation, resource, and principal context.
- Configured an audit-log export workflow using Log Router and BigQuery.
- Distinguished logged administrative actions from confirmed malicious activity.

### SQL and Audit Log Analysis

- Used BigQuery to investigate exported cloud audit records.
- Applied resource-type and method-name filters to administrative events.
- Used wildcard table names and date filters to scope audit-log analysis.
- Selected timestamp, principal, resource, and operation fields for investigation.
- Constructed a cross-service query combining Compute Engine and Cloud Storage deletion events.
- Ordered records chronologically to reconstruct an administrative activity timeline.

### Incident Investigation and Documentation

- Correlated destructive administrative operations associated with a common recorded identity.
- Distinguished scenario context from independently visible evidence.
- Identified gaps in log coverage and query-result verification.
- Evaluated the limitations of identity attribution and deletion-method observations.
- Preserved supporting screenshots and documented evidence-based conclusions.

## Conclusion

This investigation demonstrated Google Cloud administrative activity analysis using Cloud Audit Logs, Logs Explorer, Log Router, BigQuery, and SQL.

The guided exercises established a workflow for identifying bucket-deletion activity, inspecting audit metadata, exporting records, and investigating Compute Engine and Cloud Storage operations.

The independent investigation extended the analysis by correlating one VM deletion record and two bucket-deletion records into a chronological activity timeline.

All three correlated records identified the same training-account principal and occurred within approximately 37 seconds.

The strongest outcome was demonstrating how SQL-based cross-service correlation can reveal related administrative activity that may not be apparent from isolated service-specific searches.

The exercise also reinforced the importance of validating audit coverage, separating observable evidence from assumptions, and correlating administrative events with additional context before making security determinations.

These skills are applicable to cloud security monitoring, SOC investigations, incident response, and audit-log analysis.

## Evidence Index

| Figure | Evidence File | Description |
| --- | --- | --- |
| 1 | `01-cloud-storage-deletion-logs.png` | Three Cloud Storage deletion events in Logs Explorer |
| 2 | `02-expanded-audit-event.png` | Structured Cloud Storage Admin Activity event |
| 3 | `03-compute-engine-deletion-query.png` | BigQuery VM deletion query and evidence limitations |
| 4 | `04-bigquery-bucket-deletion-results.png` | Two Cloud Storage bucket-deletion records in BigQuery |
| 5 | `05-cross-service-activity-timeline.png` | Independent correlation of VM and bucket deletion operations |
| 6 (supplemental) | `06-log-router-bigquery-sink.png` | Enabled Log Router sink and BigQuery dataset destination |

All evidence screenshots are located in the accompanying [`evidence/`](evidence/) directory.
