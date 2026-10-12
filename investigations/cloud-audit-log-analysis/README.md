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