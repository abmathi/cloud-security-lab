# Google Cloud Firewall Analysis — Network Traffic and Control Validation

## Executive Summary

This investigation documents an authorized Google Cloud firewall analysis exercise involving network access controls, HTTP connectivity, VPC Flow Logs, and firewall rule logging.

The scenario involved a fictional Cymbal Bank web server running Apache within an existing Google Cloud Virtual Private Cloud (VPC) environment.

The primary objective was to assess inbound network traffic, configure firewall rules to allow and deny HTTP connections, and validate the observed network behavior through Cloud Logging.

The guided exercise included creating an ingress firewall rule allowing HTTP and SSH, generating web traffic, reviewing VPC Flow Logs, creating a rule to deny HTTP, and analyzing firewall logs.

An independent investigation extended the exercise by comparing earlier allowed traffic with later denied traffic using a broader log query.

The preserved telemetry displayed both `ALLOWED` and `DENIED` dispositions for HTTP traffic at different timestamps.

This provided evidence of a change in observed firewall enforcement behavior during the exercise.

The investigation demonstrates practical skills in cloud network security, firewall rule evaluation, security telemetry analysis, and evidence-based control validation.

## Investigation Scenario

Cymbal Bank, a fictional financial institution, maintained a demonstration Apache web server in an existing Google Cloud VPC.

The team lead, Chloe, requested an assessment of the server's inbound network access and verification that unnecessary traffic could be blocked using firewall rules.

My role was to configure the prescribed network access controls, generate test traffic, review the corresponding logs, and assess whether the configured deny rule produced the expected result.

This was an authorized training exercise, not an investigation of a confirmed unauthorized intrusion.

## Investigation Scope

| Component | Investigation Purpose |
| --- | --- |
| `vpc-net` | Existing custom-mode VPC network |
| `vpc-subnet` | Subnet with VPC Flow Logs enabled |
| `web-server` | Compute Engine VM hosting Apache |
| `http-server` | Network tag used to target firewall rules |
| `allow-http-ssh` | Ingress rule permitting TCP ports `80` and `22` |
| `deny-http` | Ingress rule denying TCP port `80` |
| VPC Flow Logs | Examine observed HTTP connections |
| Firewall Rules Logging | Evaluate firewall actions and traffic disposition |
| Logs Explorer | Query, filter, and compare connection events |

### Investigation Objectives

1. Examine the supplied web-server and VPC environment.
2. Review the configuration of HTTP and SSH firewall permissions.
3. Generate and inspect HTTP traffic using VPC Flow Logs.
4. Configure a firewall rule intended to deny inbound HTTP.
5. Analyze firewall log records for denied connections.
6. Review the firewall rule details associated with the observed traffic.
7. Independently compare earlier allowed and later denied HTTP traffic.
8. Assess the security implications and limitations of the recorded evidence.

## Evidence Sources and Methodology

### Evidence Sources

The investigation uses five preserved screenshots from Google Cloud VPC firewall configuration and Cloud Logging.

The supplied lab instructions document the environment, guided configuration tasks, and expected testing workflow.

The independent investigation notes describe a broader log query used to compare observed HTTP traffic before and after the firewall change.

### Investigation Methodology

| Phase | Analyst Activity |
| --- | --- |
| Network access configuration | Review the rules permitting HTTP and SSH |
| Traffic generation | Access the Apache web server over HTTP |
| Flow-log analysis | Examine the recorded network connection |
| Firewall modification | Introduce an ingress HTTP deny rule |
| Denied-traffic analysis | Query firewall logs for denied connections |
| Rule evaluation | Examine the connection disposition and rule details |
| Independent validation | Compare allowed and denied connection events |
| Reporting | Document observed behavior and limitations |

### Evidence Standards

The exercise was performed in an authorized, temporary Google Cloud training project.

The existing VPC, subnet, and web server were supplied as part of the exercise.

The lab instructions identify intended configurations and expected outcomes. The preserved screenshots provide evidence of selected firewall states and logged connections.

The report will distinguish between prescribed steps, observed log events, and conclusions that would require additional verification.

A firewall log recording `DENIED` supports a conclusion about the specific connection represented by that event. It does not independently establish that all possible network paths or access attempts were blocked.

Likewise, the `ALLOWED` and `DENIED` comparison documents observed traffic behavior at two points in time, rather than a complete assessment of every effective firewall policy.

## Investigation Organization

The report will document four findings:

1. Initial HTTP Network Traffic and VPC Flow Log Analysis
2. Firewall Rule Configuration and HTTP Denial
3. Denied Connection Investigation and Firewall Rule Attribution
4. Independent Before-and-After Traffic Validation

The final sections will summarize security implications, evidence limitations, skills demonstrated, and the supporting screenshot index.