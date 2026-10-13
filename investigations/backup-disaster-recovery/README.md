# Google Cloud Backup and Disaster Recovery — Cross-Project VM Restoration

## Executive Summary

This investigation documents an authorized Google Cloud backup and disaster recovery exercise involving Compute Engine virtual machines, snapshot policies, backup management, and cross-project recovery.

The fictional Cymbal Bank scenario involved restoring infrastructure following a simulated security incident.

Using Google Cloud Backup and DR, I configured a backup plan template, enrolled a Compute Engine virtual machine for protection, and performed recovery workflows within the original project and into a separate Google Cloud project.

The backup template used a continuous scheduling policy with a two-hour backup interval and two-day snapshot retention.

The preserved evidence demonstrates successful backup policy onboarding and the existence of running recovery instances in two different Google Cloud projects.

The objective was to demonstrate cloud recovery preparation, backup-policy configuration, protected workload onboarding, and infrastructure restoration across regional and project boundaries.

## Investigation Scenario

Cymbal Bank's fictional incident response team had completed containment of a simulated unauthorized-access incident and was preparing recovery actions for affected virtual machines.

The analyst was tasked with connecting to Google Cloud Backup and DR, establishing a backup policy, registering a Compute Engine instance, and restoring that instance to new locations.

The exercise included both recovery within the original project and restoration to an alternate project with the required service account permissions.

The preserved screenshots demonstrate selected backup configuration and VM recovery results, rather than independently validating the integrity or application functionality of the restored systems.

## Investigation Scope

| Component | Investigation Purpose |
| --- | --- |
| Google Cloud Backup and DR | Manage VM protection and recovery workflows |
| Backup and DR management console | Configure policies and monitor backup operations |
| Backup/recovery appliance | Support workload backup and restoration |
| Backup template `vm-backup` | Define the VM snapshot policy |
| Snapshot policy `Daily VM snapshot` | Configure continuous snapshots every two hours |
| Compute Engine `lab-vm` | Source workload for backup and recovery |
| `lab-vm-recovered` | Restored VM in the original project |
| `lab-vm-project2` | Restored VM in the alternate project |
| IAM service account roles | Support backup and recovery access across projects |
| Compute Engine VM instances | Verify the resulting restored infrastructure |

### Investigation Objectives

1. Review the Backup and DR management environment.
2. Create a backup template with an appropriate snapshot schedule and retention period.
3. Review the appliance's required service account permissions.
4. Discover and enroll `lab-vm` using the backup template.
5. Examine the protected workload onboarding result.
6. Restore a new Compute Engine instance within the original project.
7. Verify that the original and recovered VMs exist and are running.
8. Grant the required backup and recovery permissions in an alternate project.
9. Restore the workload into the alternate project.
10. Verify the resulting cross-project VM deployment and document recovery limitations.

## Evidence Sources and Methodology

### Evidence Sources

The investigation uses four preserved screenshots from the Google Cloud Backup and DR management console and the Compute Engine VM instances pages.

The lab instructions provide the authorized recovery scenario and prescribed backup, IAM, and restoration workflow.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Backup management | Review the Backup and DR console and appliance |
| Policy configuration | Define a backup plan and snapshot schedule |
| Identity review | Examine the appliance service account's required permissions |
| Workload protection | Enroll the Compute Engine instance for backup |
| Backup workflow | Review the prescribed backup creation and monitoring process |
| Same-project recovery | Mount a backup image as a new Compute Engine instance |
| Cross-project recovery | Configure target-project permissions and restore the workload |
| Verification | Inspect recovered VM instances in both projects |
| Reporting | Document evidence-supported outcomes and remaining limitations |

### Evidence Standards

The exercise was performed in an authorized, temporary Google Cloud training environment.

The lab instructions distinguish the intended configuration steps from the resulting cloud resources. The preserved screenshots establish selected policy settings, successful workload onboarding, and running recovered VM instances.

The backup policy schedule represents the configured backup frequency. It does not independently establish the actual timing or success of every scheduled backup.

Likewise, a recovered VM displaying Running status demonstrates that the instance is operational at the Compute Engine level, but it does not prove that applications, data, or dependent services were successfully restored.

The report will distinguish between procedures prescribed by the lab and results independently supported by the preserved evidence.

## Investigation Organization

The report will document four findings:

1. Backup Plan and Snapshot Policy Configuration
2. Compute Engine Workload Onboarding and Backup Protection
3. Same-Project Compute Engine Recovery Verification
4. Cross-Project Compute Engine Disaster Recovery

The final sections will summarize security implications, recovery limitations, recommendations, skills demonstrated, and supporting evidence.

## Investigation Findings

### Finding 1 — Backup Plan and Snapshot Policy Configuration

#### Investigation Context

The first phase of the Cymbal Bank disaster recovery exercise involved creating a backup plan template for Google Cloud Compute Engine virtual machines.

The fictional incident scenario required preparing a recovery mechanism that could support restoration of cloud infrastructure following a security incident or operational disruption.

The objective was to define a repeatable backup policy, examine its snapshot frequency and retention settings, and establish the intended protection configuration before enrolling the source virtual machine.

#### Backup Template Configuration

I opened the Google Cloud Backup and DR management console and navigated to:

`Backup Plans → Templates → Create Template`

I configured a backup template named:

`vm-backup`

The template description was:

`Virtual Machine Backups`

I then added a Production-to-Snapshot backup policy.

The preserved screenshot showed the following settings:

| Backup Configuration | Observed Value |
| --- | --- |
| Template name | `vm-backup` |
| Description | `Virtual Machine Backups` |
| Policy name | `Daily VM snapshot` |
| Policy type | Snapshot |
| Scheduling mode | Continuous |
| Repeat interval | Every 2 hours |
| Snapshot expiration | After 2 days |
| Policy ID | `4363` |
| Policy setting overrides | Yes |
| Snapshot policies displayed | 1 |

![Google Cloud Backup and DR snapshot policy](evidence/01-backup-template-snapshot-policy.png)

*Figure 1 — Google Cloud Backup and DR template `vm-backup`, showing the `Daily VM snapshot` policy configured for continuous scheduling every two hours, with a two-day expiration setting.*

The screenshot showed one snapshot policy associated with the backup template.

The configuration also permitted policy-setting overrides.

The policy's intended backup schedule was every two hours, with snapshots configured to expire after two days.

#### Backup Frequency and Retention Analysis

The policy name, `Daily VM snapshot`, could suggest a once-per-day backup schedule.

However, the actual configuration specified:

- Scheduling: `Continuous`
- Repeat value: `Every 2 hours`
- Expire after: `2 days`

Therefore, the intended backup interval was two hours rather than twenty-four hours.

The configured expiration setting established a two-day retention period for snapshots produced under the policy, subject to the service's actual retention behavior and successful backup execution.

| Recovery Planning Attribute | Configured Value | Security Relevance |
| --- | --- | --- |
| Backup scheduling mode | Continuous | Supports recurring backup execution |
| Backup repeat interval | Every 2 hours | Defines the intended frequency of recovery-point creation |
| Snapshot expiration | 2 days | Limits how long snapshots are retained under the policy |
| Policy overrides | Enabled | Permits applicable policy settings to be overridden |

A more frequent backup schedule can reduce the amount of recent data potentially lost during recovery when backups complete successfully.

However, the two-hour interval does not independently establish a guaranteed two-hour Recovery Point Objective (RPO).

The actual recoverable point depends on successful backup execution, backup timing, and the availability and integrity of the resulting recovery images.

Similarly, the two-day retention setting does not establish a Recovery Time Objective (RTO).

RTO depends on the time needed to restore systems and resume required services.

#### Backup Policy Verification

The preserved screenshot displayed the configured snapshot policy within the backup template editor.

It showed a **Save Template** button, indicating that the interface was displaying the configuration prior to, or during, the template-saving workflow.

The image did not independently establish that the template had already been saved or that the scheduled backups had executed successfully.

Subsequent evidence from the workload onboarding and recovery stages would provide additional information about use of the configured backup template.

#### Security Relevance

Backup policies contribute to organizational resilience by defining how protected workloads are backed up and how long recovery images remain available.

In an incident response context, accessible and trustworthy recovery points may help restore operations following accidental deletion, system failure, ransomware, or another disruptive event.

However, backup configuration alone is not equivalent to validated recoverability.

Effective disaster recovery also requires verifying that backups complete successfully, recovery images remain usable, access controls protect backup infrastructure, and restored systems meet operational requirements.

Retention policies should also reflect business, legal, and security requirements rather than relying solely on a training environment's short retention period.

#### Analyst Assessment

**Assessment: Continuous snapshot backup policy configured with a two-hour repeat interval and two-day expiration setting.**

The preserved evidence showed a backup template named `vm-backup` containing the `Daily VM snapshot` policy.

Despite the policy name, the configured schedule specified continuous backup execution every two hours.

The screenshot established the intended backup frequency and retention settings but did not independently confirm successful template saving, scheduled backup execution, or recovery-point integrity.

The next phase examines enrollment of the source Compute Engine instance into the configured backup protection workflow.