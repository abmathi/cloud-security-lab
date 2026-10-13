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

The report will document the following investigation stages:

1. Administrative Activity Generation and Audit Log Inspection
2. Audit Log Export and BigQuery Configuration
3. Compute Engine Deletion Analysis
4. Cloud Storage Deletion Analysis
5. Independent Cross-Service Activity Correlation

The final sections will summarize security implications, evidence limitations, skills demonstrated, and supporting evidence.

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

*Figure 1 — Google Cloud Logs Explorer displaying three Admin Activity records filtered by `storage.googleapis.com` and `storage.buckets.delete`. The results identify bucket-deletion operations and their timestamps.*

All three records were associated with Cloud Storage bucket-deletion activity.

The results included event summaries and principal-identification fields. However, the screenshot did not expand the individual records sufficiently to establish every affected resource or fully verify the principal associated with each event.

The visible records supported identification of the deletion method and event sequence, but further field inspection would be required for a complete attribution assessment.

#### Security Relevance

Cloud Storage bucket deletion is a destructive administrative operation that can affect resource availability and data retention.

In a security investigation, such events may warrant review of the acting principal, affected resource, authorization context, and related administrative activity.

However, the presence of bucket-deletion records does not independently establish malicious intent or unauthorized access.

In this exercise, the operations were generated in an authorized test environment to reproduce a simulated incident.

#### Analyst Assessment

**Assessment: Three Cloud Storage bucket-deletion audit records identified.**

Logs Explorer successfully isolated administrative activity associated with the `storage.buckets.delete` method.

The events provided a starting point for investigating destructive cloud-resource activity and demonstrated the value of filtering audit records by service and operation.

The next stages will examine log export, BigQuery analysis, and correlation across multiple cloud resource types.