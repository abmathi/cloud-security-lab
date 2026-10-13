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

## Investigation Findings

### Finding 1 — Security Command Center Finding Identification

#### Investigation Context

The first phase involved reviewing Google Cloud Security Command Center (SCC) for findings associated with user-managed service account authentication keys.

The training scenario intentionally introduced a user-managed JSON key for a test service account. The objective was to determine whether Security Command Center identified the resulting configuration risk.

#### Security Command Center Findings Review

I opened Security Command Center, navigated to the Findings interface, and filtered the findings list by the following category:

`User managed service account key`

The interface displayed three matching findings within the selected seven-day window.

| Finding | Category | Severity | Event Date |
| --- | --- | --- | --- |
| 1 | User managed service account key | Medium | September 21, 2026 |
| 2 | User managed service account key | Medium | September 21, 2026 |
| 3 | User managed service account key | Medium | September 14, 2026 |

![Security Command Center service account key findings](evidence/01-scc-service-account-findings.png)

*Figure 1 — Google Cloud Security Command Center displaying three medium-severity findings in the User managed service account key category, filtered within the selected seven-day window.*

Each displayed finding had an attack exposure score of `0`.

The list also showed the findings as unmuted. The visible records included event and creation timestamps, allowing the analyst to distinguish recently observed findings from older records.

The list alone did not establish whether the three findings involved the same service account, separate resources, or repeated observations of similar configurations.

#### Initial Triage Assessment

The category indicated the presence of user-managed service account keys, which can introduce credential-management risks.

Unlike automatically managed credentials, exported user-managed keys can potentially be copied, stored insecurely, or retained beyond their intended lifetime.

An exposed key may allow someone possessing it to authenticate as the associated service account, subject to the account's effective permissions and other applicable controls.

However, the presence of a user-managed key does not independently establish credential compromise.

Similarly, an attack exposure score of `0` does not prove that the underlying configuration is harmless or that the associated identity cannot be abused.

#### Analyst Assessment

**Assessment: Three medium-severity user-managed service account key findings identified in Security Command Center.**

The initial triage confirmed that Security Command Center detected the relevant credential-management category.

The available evidence supported further investigation of affected service accounts, key status, associated permissions, and remediation options.

The next phase examines the details of a selected finding to determine the affected resource and recommended corrective action.