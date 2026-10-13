# Google Cloud Security Lab — Detection, Hardening, and Incident Response

A collection of seven hands-on Google Cloud security investigations focused on threat detection, security monitoring, infrastructure hardening, incident response, and disaster recovery.

Each investigation documents a practical security scenario, the tools and procedures used, observed results, supporting screenshots, and an evidence-based analyst assessment.

The goal of this repository is to demonstrate applied cloud security and security operations skills through documented investigation workflows rather than theoretical exercises alone.

## Investigation Portfolio

| Investigation | Security Focus | Key Demonstration |
| --- | --- | --- |
| [01 — Cloud Audit Log Analysis](investigations/cloud-audit-log-analysis/README.md) | Cloud logging and monitoring | Investigating Google Cloud audit activity and security-relevant events |
| [02 — Security Command Center Triage](investigations/security-command-center-triage/README.md) | Security findings and prioritization | Reviewing, classifying, and assessing cloud security findings |
| [03 — Cloud Threat Detection](investigations/cloud-threat-detection/README.md) | IAM and threat detection | Analyzing cloud threat findings and identity-related activity |
| [04 — Cloud Incident Response](investigations/cloud-incident-response/README.md) | Incident response and remediation | Investigating cloud misconfigurations, hardening infrastructure, and comparing security findings |
| [05 — Terraform Firewall Security](investigations/terraform-firewall-security/README.md) | Infrastructure as Code | Removing unnecessary firewall port allowances and verifying the deployed configuration |
| [06 — Cloud Firewall Analysis](investigations/cloud-firewall-analysis/README.md) | Network security monitoring | Correlating firewall rules with allowed and denied traffic events |
| [07 — Backup and Disaster Recovery](investigations/backup-disaster-recovery/README.md) | Resilience and recovery | Documenting VM recovery in the original project and an alternate Google Cloud project |

## Selected Technical Highlights

### Cloud Threat Detection and Security Monitoring

Investigated cloud security findings using Google Cloud Security Command Center and Cloud Logging.

Reviewed security-relevant events, identity activity, configuration weaknesses, and available telemetry to develop evidence-backed findings.

### Infrastructure Hardening

Used Terraform to modify an existing Google Cloud firewall rule.

The original configuration permitted TCP ports `80`, `8080`, and `1000-2000`.

After identifying the broad range as unnecessary under the exercise's assumed application requirements, I modified the configuration and verified that the deployed firewall retained only TCP ports `80` and `8080`, alongside ICMP.

### Firewall Enforcement Validation

Analyzed VPC Flow Logs and Firewall Rules Logging to investigate HTTP traffic.

Identified overlapping allow and deny rules at priority `1000`, attributed a denied HTTP connection to the `deny-http` firewall rule, and independently compared earlier `ALLOWED` and later `DENIED` events.

### Incident Response and Cloud Remediation

Examined Compute Engine, Cloud Storage, and VPC firewall security findings in a simulated cloud data-breach scenario.

Verified that a Cloud Storage bucket changed from publicly accessible to nonpublic with Public Access Prevention and uniform bucket-level access enabled.

Identified a discrepancy between intended restricted SSH access and an actual firewall Deny action targeting the IAP source range.

### Backup and Disaster Recovery

Configured a backup template specifying a two-hour snapshot interval and two-day expiration period.

Documented successful workload onboarding and verified running recovery instances in both the original Google Cloud project and an alternate project and region.

The investigation distinguishes infrastructure-level restoration from fully validated application recovery and achieved RPO/RTO.

## Tools and Technologies

| Category | Technologies |
| --- | --- |
| Cloud platform | Google Cloud Platform |
| Security monitoring | Security Command Center, Cloud Logging |
| Network security | VPC firewall rules, VPC Flow Logs, Firewall Rules Logging |
| Infrastructure as Code | Terraform, HCL, Google Cloud Shell |
| Compute security | Compute Engine, IAM, service accounts |
| Storage security | Cloud Storage, Public Access Prevention, uniform bucket-level access |
| Recovery | Google Cloud Backup and DR, snapshots, cross-project VM restoration |
| Security frameworks | PCI DSS control mapping and cloud security best practices |
| Documentation | Markdown, Git, GitHub, screenshot-based evidence |

## Investigation Methodology

The investigations follow an evidence-driven security analysis workflow:

1. **Scenario and scope** — Identify the environment, security objective, and relevant assets.
2. **Evidence collection** — Examine configurations, cloud findings, logs, or recovery results.
3. **Technical analysis** — Interpret the observed behavior and associated security implications.
4. **Security action** — Document the guided remediation or independently performed investigation.
5. **Verification** — Compare intended changes against observable results.
6. **Analyst assessment** — State supported conclusions and remaining uncertainties.
7. **Reporting** — Preserve screenshots, document findings, and recommend further validation where necessary.

Each investigation contains its own `README.md` and an `evidence/` directory.

## Evidence and Scope

These investigations were performed in authorized Google Cloud training environments using fictional organizational scenarios.

The reports distinguish between prescribed lab procedures, directly observed technical evidence, and additional verification that would be necessary in production.

A simulated breach is not presented as a confirmed real-world intrusion. Likewise, a configured security control is not automatically treated as effective without supporting evidence.

Screenshots, logs, and cloud configuration observations are used to substantiate findings wherever available.

## Repository Structure

```text
cloud-security-lab/
├── README.md
└── investigations/
    ├── cloud-audit-log-analysis/
    ├── security-command-center-triage/
    ├── cloud-threat-detection/
    ├── cloud-incident-response/
    ├── terraform-firewall-security/
    ├── cloud-firewall-analysis/
    └── backup-disaster-recovery/
```

Each investigation directory contains its own technical report and supporting evidence.

## Portfolio

Additional cybersecurity projects involving SOC monitoring, digital forensics, network security, identity and access management, and AWS security are available through my GitHub profile.

- **GitHub:** [github.com/abmathi](https://github.com/abmathi)
- **Portfolio website:** [austinsangle.com](https://austinsangle.com)

---

*These projects are intended to demonstrate practical cloud security investigation, technical validation, and professional security documentation.*