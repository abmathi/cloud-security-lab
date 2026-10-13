# Google Cloud Terraform Firewall Security — Infrastructure as Code Hardening

## Executive Summary

This investigation demonstrates the use of Terraform to provision and harden Google Cloud network infrastructure.

The scenario involved deploying a Virtual Private Cloud (VPC) network and associated firewall rule for a fictional banking application's cloud environment.

Using Google Cloud Shell and Terraform, I examined the supplied infrastructure configuration, reviewed the initial deployment plan, and verified the resulting VPC firewall rule in the Google Cloud console.

An independent security review identified an unnecessarily broad TCP port range in the original firewall configuration.

I modified the Terraform code to remove TCP ports `1000-2000` while preserving ports `80` and `8080`, reviewed the resulting Terraform execution plan, and verified that the deployed firewall reflected the updated configuration.

The objective was to demonstrate repeatable infrastructure provisioning, firewall rule analysis, least-privilege network configuration, and evidence-based change validation through Infrastructure as Code.

## Investigation Scenario

Cymbal Bank, a fictional financial organization, required a new Google Cloud VPC network and firewall configuration to support deployment of a banking application.

The infrastructure team chose Terraform to define and provision these resources in a repeatable way.

My role was to examine the network configuration, deploy the infrastructure, verify the resulting firewall behavior, and independently assess whether the firewall permitted unnecessary traffic.

The independent hardening step assumed that the application required only TCP ports `80` and `8080`.

The preserved evidence documents configuration and deployment changes rather than network exploitation or a confirmed security incident.

## Investigation Scope

| Component | Investigation Purpose |
| --- | --- |
| Terraform | Define and manage cloud infrastructure as code |
| Google Cloud Shell | Execute Terraform commands |
| Google Cloud VPC | Provide an isolated network resource |
| VPC firewall rule | Control permitted network traffic |
| `main.tf` | Define the desired infrastructure configuration |
| `terraform plan` | Preview proposed infrastructure changes |
| `terraform apply` | Deploy approved changes |
| Google Cloud console | Verify deployed firewall settings |

### Investigation Objectives

1. Review the supplied Terraform configuration for the VPC and firewall.
2. Identify the protocols and ports permitted by the original firewall rule.
3. Review the initial Terraform infrastructure deployment plan.
4. Verify the deployed firewall configuration in Google Cloud.
5. Independently identify an unnecessarily broad TCP port range.
6. Modify the Terraform configuration to retain only the assumed required application ports.
7. Review the planned infrastructure update.
8. Apply and verify the firewall change.
9. Document the security implications and limitations of the observed configuration.

## Evidence Sources and Methodology

### Evidence Sources

The investigation uses five preserved screenshots of Terraform configuration, Terraform execution plans, and Google Cloud VPC firewall settings.

The original exercise instructions document the guided Terraform provisioning workflow.

The independent investigation notes document the decision to remove an unnecessary TCP port range and validate the resulting infrastructure change.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Configuration review | Examine the initial `main.tf` |
| Infrastructure planning | Review the initial Terraform plan |
| Deployment verification | Inspect the initial firewall in Google Cloud |
| Security assessment | Identify unnecessary permitted TCP ports |
| Code modification | Remove the broad port range from Terraform |
| Change planning | Review the in-place Terraform update |
| Change application | Apply the modified configuration |
| Final verification | Inspect the deployed firewall rule |
| Reporting | Document the change and evidence limitations |

### Evidence Standards

The work was completed within an authorized, temporary Google Cloud training environment.

The original Terraform configuration came from a supplied example repository.

The independent hardening decision was based on assumed application requirements and was not derived from production traffic analysis.

The firewall's `source_tags = ["web"]` configuration is an important scope limitation: the initial firewall rule should not be described as unrestricted public internet exposure.

The preserved Terraform plan and Google Cloud firewall screenshots support conclusions about the planned update and final visible configuration. They do not independently demonstrate application connectivity or the complete effective network security posture.

## Investigation Organization

The report will document four findings:

1. Terraform VPC and Firewall Configuration Review
2. Initial Infrastructure Planning and Deployment Verification
3. Independent Firewall Hardening Through Terraform
4. Post-Change Firewall Configuration Verification

The final sections will summarize security implications, evidence limitations, skills demonstrated, and supporting screenshots.