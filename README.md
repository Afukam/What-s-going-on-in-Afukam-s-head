# Afukam's Project Journal

A public work log for ideas, experiments, and projects I'm developing. This repository is where I connect the dots between what I'm learning, what I'm building, and what I want to investigate next.

The focus here is on documenting progress honestly: what is complete, what is underway, what I learned, and what remains to be done.

## Current Featured Project

### AWS Cowrie Honeypot + Wazuh SIEM Lab

An AWS-hosted security monitoring lab using Cowrie to emulate an SSH service and Wazuh for security monitoring and investigation.

**Current status:** In progress — Wazuh deployment and agent onboarding have been completed to the point of accessing the dashboard and confirming an agent is running. The next milestone is verifying that the intended Cowrie telemetry reaches Wazuh and is searchable.

- [Project phases, progress log, and next milestones](./AWS-Cowrie-Wazuh-SIEM-Lab.md)
- [Evidence plan and completion checklist](./EVIDENCE-PLAN.md)
- [Detection development workflow](./detections/README.md)

## Other Ideas and Notes

This repository can also hold research questions, learning notes, project ideas, and reflections that don't yet belong in a finished portfolio project.

For completed or more structured cybersecurity work, see the [Cybersecurity Projects portfolio](https://github.com/Afukam/Cybersecurity-Projects).

## Documentation Principles

- Separate verified results from planned work.
- Record the environment and tools used.
- Never publish credentials, private keys, tokens, or sensitive access details.
- Sanitize public IP addresses, hostnames, usernames, and logs where appropriate.
- Include limitations and next steps rather than overstating results.
