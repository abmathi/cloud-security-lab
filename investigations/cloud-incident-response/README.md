# Google Cloud Incident Response — Data Breach Remediation and Recovery

## Executive Summary

This investigation documents a simulated cloud data breach response involving misconfigured Google Cloud infrastructure at Cymbal Retail.

The exercise placed me in the role of a junior security analyst supporting incident assessment, containment, system recovery, security hardening, and compliance verification.

Using Google Cloud Security Command Center and related cloud services, I investigated configuration findings affecting Compute Engine, Cloud Storage, and VPC firewall rules.

The response workflow included recovering a virtual machine from a pre-incident snapshot, restricting public Cloud Storage access, reducing unnecessary network exposure, enabling firewall rule logging, and reviewing PCI DSS 3.2.1 compliance findings.

The objective was to demonstrate a structured approach to identifying cloud security weaknesses, prioritizing remediation, and evaluating the observable results of corrective actions.

## Incident Scenario

Cymbal Retail, a fictional organization processing customer payment information, experienced a simulated data breach involving unauthorized access to sensitive customer records.

The training scenario described exposed network services, vulnerable cloud resources, and publicly accessible storage as concerns requiring investigation.

The role of the security analyst was to identify relevant misconfigurations, support containment and recovery, and assess whether remediation improved the security posture of the environment.

The scenario's breach narrative provides investigation context. The screenshots document selected infrastructure configurations and findings rather than independent proof of an actual intrusion or data exfiltration.

## Investigation Scope

| Component | Investigation Purpose |
| --- | --- |
| Security Command Center | Identify cloud vulnerabilities and misconfigurations |
| Compute Engine | Review exposed VM configuration and recovery actions |
| Persistent Disk snapshots | Support recovery of a VM from a prior snapshot |
| Cloud Storage | Assess and restrict public bucket access |
| VPC firewall rules | Reduce unnecessary remote-access exposure |
| Identity-Aware Proxy | Preserve restricted SSH administration |
| Firewall rule logging | Improve network traffic visibility |
| PCI DSS 3.2.1 report | Review security-control findings before and after remediation |

### Investigation Objectives

1. Examine security findings associated with the simulated data breach.
2. Identify exposed infrastructure, storage, and network access configurations.
3. Review relevant PCI DSS 3.2.1 findings.
4. Document recovery of the affected Compute Engine virtual machine.
5. Evaluate remediation of public Cloud Storage access.
6. Restrict SSH administration using the Google Cloud IAP source range.
7. Document removal of unnecessary default firewall rules and enable logging.
8. Compare compliance findings before and after remediation.
9. Identify unresolved issues and limitations in the preserved evidence.

## Evidence Sources and Methodology

### Evidence Sources

The investigation uses ten preserved screenshots from Google Cloud Security Command Center, Compute Engine, Cloud Storage, VPC firewall configuration, and PCI DSS compliance reporting.

The lab instructions provide the authorized incident scenario and prescribed remediation workflow.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Assessment | Review security findings and affected cloud resources |
| Prioritization | Identify significant exposure and misconfiguration risks |
| Containment | Reduce external access to vulnerable cloud resources |
| Recovery | Follow the Compute Engine snapshot-based restoration workflow |
| Storage remediation | Restrict public access and review bucket permissions |
| Network hardening | Limit administrative access and update firewall rules |
| Visibility | Enable firewall rule logging where required |
| Verification | Reexamine compliance findings and note remaining limitations |
| Reporting | Document evidence, remediation actions, and conclusions |

### Evidence Standards

The investigation was performed in an authorized, temporary training project.

The data breach was part of the fictional exercise scenario. The preserved screenshots do not independently establish real-world compromise, malware execution, or data theft.

The report will distinguish between remediation steps described in the lab instructions and final configurations directly supported by screenshots.

A restored VM cannot be declared malware-free solely because it was created from a snapshot. Similarly, a reduction in compliance findings does not independently demonstrate full regulatory compliance.

## Investigation Organization

The report will document the following stages:

1. Initial Cloud Security Posture Assessment
2. PCI DSS Compliance Findings Review
3. Compute Engine Vulnerability Assessment and Recovery
4. Cloud Storage Access Remediation
5. Restricted SSH Access Configuration
6. Firewall Hardening and Logging
7. Post-Remediation Compliance Verification

The final sections will summarize security implications, remediation outcomes, evidence limitations, skills demonstrated, and supporting evidence.

## Investigation Findings

### Finding 1 — Initial Cloud Security Posture Assessment

#### Investigation Context

The first phase of the simulated Cymbal Retail incident response involved evaluating existing cloud security misconfigurations.

The exercise scenario described a breach involving customer information, but the initial task focused on identifying vulnerable or misconfigured cloud resources using Google Cloud Security Command Center (SCC).

The objective was to identify the affected resource categories, evaluate the severity distribution, and prioritize subsequent investigation and remediation activities.

#### Security Command Center Risk Overview

I opened Security Command Center and reviewed the **Misconfigurations** dashboard using the **By resource type** view.

The dashboard displayed the following findings:

| Resource Type | Critical | High | Medium | Low |
| --- | ---: | ---: | ---: | ---: |
| Firewall | 0 | 2 | 4 | 0 |
| `compute.Instance` | 0 | 1 | 3 | 0 |
| Bucket | 0 | 1 | 1 | 1 |
| `resourcemanager.Project` | 0 | 0 | 2 | 9 |
| Network | 0 | 0 | 2 | 0 |
| `compute.Project` | 0 | 0 | 1 | 0 |
| `ServiceAccountKey` | 0 | 0 | 1 | 0 |
| Subnetwork | 0 | 0 | 0 | 48 |
| **Total** | **0** | **4** | **14** | **58** |

![Initial SCC misconfiguration overview](evidence/01-initial-scc-misconfiguration-overview.png)

*Figure 1 — Google Cloud Security Command Center misconfigurations grouped by resource type, showing High-severity findings affecting firewall rules, Compute Engine instances, and Cloud Storage buckets.*

The resource-type breakdown contained no Critical findings but showed four High-severity misconfiguration findings.

Firewall, Compute Engine instance, and Cloud Storage bucket categories accounted for the High-severity findings.

The dashboard also displayed Medium- and Low-severity findings affecting several other resource types.

The findings counts represent the visible security-check results grouped by resource type. They do not independently establish the number of distinct affected assets, successful exploitation, or direct involvement in the simulated breach.

#### Initial Risk Prioritization

The initial review identified three resource categories for priority investigation.

| Priority | Resource Category | Investigation Rationale |
| --- | --- | --- |
| High | Firewall | Two High-severity findings indicated potentially significant network access misconfigurations |
| High | Compute Engine | One High-severity instance finding warranted examination of VM exposure and recovery requirements |
| High | Cloud Storage | One High-severity bucket finding warranted investigation of storage access controls |

These priorities were based on the severity distribution and the incident scenario's known remediation requirements.

The dashboard alone did not establish the specific technical cause of each finding or whether the affected resources were exploited.

Further finding-level investigation was therefore required before attributing the simulated data breach to a particular configuration weakness.

#### Security Relevance

Security Command Center provides an aggregated view of security misconfigurations across supported Google Cloud resources.

Grouping findings by resource type helps analysts identify areas that may require urgent review.

However, finding severity should be evaluated together with exposure, affected data, resource criticality, and operational context.

A High-severity configuration finding is not equivalent to a confirmed attack or verified breach path.

#### Analyst Assessment

**Assessment: Four High-severity misconfiguration findings identified across Firewall, Compute Engine, and Cloud Storage.**

The initial SCC review established a baseline of visible cloud security findings and identified resource categories requiring further investigation.

The results supported prioritization of network controls, virtual machine security, and storage permissions.

The next phase examines PCI DSS compliance findings to identify additional control weaknesses relevant to the simulated incident.