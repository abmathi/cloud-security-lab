# Google Cloud Security Command Center — Service Account Finding Triage and Remediation

## Executive Summary

This investigation examines a Google Cloud Security Command Center (SCC) finding involving user-managed service account credentials.

The exercise recreated a security finding by configuring a test service account with project-level Owner privileges, generating a user-managed JSON authentication key, and activating the service account in Cloud Shell.

Using a separate training account, I investigated the resulting finding in Security Command Center and followed the remediation procedure to delete the authentication key.

An independent IAM review extended the investigation by examining the service account's remaining access permissions. This review identified project-level Owner access as a separate security concern that was not resolved by deleting the key.

The investigation demonstrates security finding triage, credential-risk remediation, IAM privilege assessment, and evidence-based validation in an authorized Google Cloud training environment.

## Investigation Scope

The investigation was conducted in a temporary Google Cloud lab project using controlled service account activity.

| Component | Investigation Purpose |
| --- | --- |
| Security Command Center | Identify and investigate service account security findings |
| IAM | Review service account access and permissions |
| Service Accounts | Examine the affected test identity |
| User-managed JSON key | Reproduce the credential-management finding |
| Cloud Shell | Activate and verify the test service account |
| SCC finding details | Assess severity, affected resources, and recommended actions |
| Service account Keys | Verify removal of the user-managed key |
| Principals with access | Independently examine remaining Owner-level permissions |

### Investigation Objectives

1. Reproduce a user-managed service account key finding in a controlled environment.
2. Identify the finding in Google Cloud Security Command Center.
3. Examine the finding's category, severity, affected resource, and recommended action.
4. Remove the user-managed key as directed by the lab procedure.
5. Verify the state of the service account's Keys tab.
6. Independently review the service account's IAM permissions.
7. Determine whether deleting the key addresses all identified risks.
8. Document findings, security implications, and evidence limitations.

## Evidence Sources and Methodology

### Evidence Sources

This investigation uses screenshots from Security Command Center, service account key management, and IAM access review.

The evidence was collected during a guided Google Cloud security exercise and an additional independent IAM assessment.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Preparation | Establish a test service account in an authorized cloud project |
| Finding generation | Create and activate a user-managed service account key |
| Triage | Identify and inspect the resulting SCC finding |
| Remediation | Delete the service account key |
| Validation | Inspect the Keys tab after remediation |
| Independent assessment | Examine project-level permissions associated with the service account |
| Reporting | Document observed evidence, remaining risks, and recommended controls |

### Evidence Standards

The finding was intentionally generated within a training environment.

The exercise described the alert as a false positive because the activity was authorized and performed for testing. However, the underlying user-managed credential and broad IAM permissions represented genuine security concerns within that configuration.

This report therefore distinguishes the benign context of the activity from the validity of the detected configuration risk.

The investigation also distinguishes observed changes from recommended production remediation. In particular, deleting the JSON key does not automatically remove the service account's IAM role assignments.

## Investigation Organization

The report documents four stages:

1. Security Command Center Finding Identification
2. Service Account Finding Analysis
3. User-Managed Key Remediation Verification
4. Independent IAM Excessive Privilege Assessment

The final sections will address security implications, remediation recommendations, evidence limitations, skills demonstrated, and supporting screenshots.