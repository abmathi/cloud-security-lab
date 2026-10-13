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

### Finding 2 — Service Account Credential Risk Analysis

#### Investigation Context

After identifying three user-managed service account key findings, I opened the details of a recent Security Command Center finding to investigate the affected resource.

The objective was to determine what Security Command Center detected, identify the associated service account, assess the severity, and review the recommended corrective action.

#### Finding Details

The Security Command Center summary identified the following information:

| Field | Observed Value |
| --- | --- |
| Finding category | `User managed service account key` |
| Finding state | Active |
| Severity | Medium |
| Event time | September 21, 2026, 3:58:51 PM GMT-7 |
| Creation time | September 21, 2026, 3:58:52 PM GMT-7 |
| Resource type | `google.iam.ServiceAccountKey` |
| Affected service account | `test-account` |
| Cloud provider | Google Cloud |
| Security contacts | None listed |

![Security Command Center user-managed service account key finding](evidence/02-scc-finding-details.png)

*Figure 2 — Security Command Center finding details identifying an active, medium-severity user-managed service account key associated with the test service account. The panel displays the affected credential resource and recommended corrective actions.*

The affected resource was a user-managed service account key associated with the test identity created earlier in the lab.

Security Command Center described the concern as the presence of a user-managed key that could be leaked.

The finding did not indicate that a credential leak, attacker authentication, or account compromise had occurred.

#### Risk Analysis

User-managed service account keys are long-lived credentials unless explicitly rotated, revoked, or deleted.

Because the private key material can be exported, user-managed keys introduce risks associated with insecure storage, accidental disclosure, credential reuse, and unauthorized access if compromised.

The potential impact depends partly on the privileges granted to the service account.

In this exercise, the test service account had been assigned project-level Owner permissions.

This combination is important because a compromised credential associated with a highly privileged service account could provide extensive administrative capabilities within the project's permission boundaries.

However, the screenshot only confirms the presence of a user-managed key and the associated finding. It does not establish that the key was compromised or that its permissions were abused.

#### Recommended Corrective Action

The SCC finding provided two next steps:

1. Open the affected service account's details page.
2. Delete user-managed service account keys that are not used by any application.

This recommendation addresses the existence of unnecessary user-managed credential material.

Before deleting a key in a production environment, an analyst should identify dependent workloads and coordinate with the relevant service owners to prevent unintended service disruption.

Where possible, applications should use short-lived credentials or supported workload identity mechanisms rather than downloadable private keys.

#### False Positive Classification

The lab scenario characterized this finding as a false positive because the service account and key were intentionally created for authorized testing.

That classification describes the benign context of the activity.

It does not mean that Security Command Center incorrectly identified the existence of the user-managed key.

The finding accurately represented a configuration risk introduced during the exercise.

An analyst should therefore distinguish between a finding that is technically accurate and activity that is not associated with an actual security incident.

#### Analyst Assessment

**Assessment: Active, medium-severity user-managed service account key finding confirmed for the test service account.**

The investigation identified the affected credential resource, documented the associated risk, and reviewed the remediation guidance provided by Security Command Center.

The evidence supports a credential-management concern rather than a confirmed security compromise.

The next investigation phase verifies whether the unnecessary user-managed key was removed from the service account.

### Finding 3 — User-Managed Service Account Key Remediation

#### Investigation Context

After examining the active Security Command Center finding, I followed the lab remediation procedure to remove the user-managed JSON authentication key associated with the `test-account` service account.

The objective was to address the credential-management risk and verify the resulting key inventory through the Google Cloud console.

#### Remediation Procedure

The remediation focused on deleting the unnecessary user-managed service account key.

The procedure involved:

1. Navigating to IAM & Admin → Service Accounts.
2. Opening the `test-account` service account.
3. Selecting the **Keys** tab.
4. Deleting the user-managed authentication key.
5. Reviewing the Keys tab to verify the remaining key inventory.

Deleting the key revokes that service account key as a Google Cloud authentication credential.

This does not automatically delete copies of the downloaded JSON file or change IAM roles assigned to the service account.

#### Post-Remediation Verification

After completing the deletion procedure, I reviewed the service account's Keys tab.

| Verification Item | Observed Result |
| --- | --- |
| Affected identity | `test-account` |
| Console location | Service Accounts → Keys |
| Displayed key records | No rows to display |
| Remaining user-managed keys | None visible in the captured inventory |
| IAM role changes | Not demonstrated by this screenshot |
| SCC finding resolution | Not independently demonstrated by this screenshot |

![Service account key inventory after remediation](evidence/03-service-account-key-remediation.png)

*Figure 3 — Google Cloud IAM service account Keys tab showing no key records for `test-account` following the remediation procedure.*

The screenshot confirmed that no user-managed keys were listed for the affected service account at the time of verification.

This supports the conclusion that the key-removal procedure addressed the visible credential-management configuration.

However, no subsequent SCC finding-status screenshot was preserved. Therefore, the evidence does not independently establish that the original finding had transitioned to an inactive or resolved state.

#### Security Relevance

User-managed service account keys can become security risks when they are stored insecurely, exposed in source repositories, shared unnecessarily, or retained after their intended use.

Removing an unnecessary key eliminates that specific credential's ability to authenticate to Google Cloud.

For production environments, organizations should prefer supported short-lived or keyless authentication mechanisms, including attached service account identities or Workload Identity Federation where appropriate.

Organizations can also implement policies restricting user-managed key creation to reduce unnecessary credential exposure.

Before key deletion in a production environment, dependencies should be reviewed to avoid interrupting legitimate applications.

#### Residual Risk

Deleting a user-managed service account key addresses the credential-management finding, but it does not automatically reduce the permissions assigned to the service account.

In this exercise, the account had also been granted project-level Owner access.

The next investigation phase independently examines these permissions to determine whether a separate excessive-privilege concern remained after key deletion.

#### Analyst Assessment

**Assessment: User-managed service account key removed from the visible key inventory.**

The service account's Keys tab displayed no remaining key records following the remediation procedure.

This verified the post-remediation state of the key inventory, but did not independently confirm SCC finding closure or changes to the account's IAM permissions.

Further review was therefore warranted to assess remaining privilege-related risks.