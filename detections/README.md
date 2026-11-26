# Cowrie + Wazuh Detection Work

This directory will contain detection documentation and sanitized rule exports after the log pipeline is verified.

## Required workflow

1. Confirm Cowrie JSON events are collected by the intended Wazuh agent.
2. Inspect actual event fields and decoder output; do not guess field names or rule IDs.
3. Define a detection hypothesis and its limits.
4. Write the rule/query using fields confirmed in the captured telemetry.
5. Generate controlled test activity.
6. Capture the matching event and resulting alert.
7. Assess false positives, gaps, and tuning.
8. Record the result using the [SIEM detection validation template](https://github.com/Afukam/SIEM-lab-series/blob/main/DETECTION-VALIDATION-TEMPLATE.md).

## Candidate detections

- Repeated failed logins within a defined interval.
- Suspicious command input in a Cowrie session.
- A successful login after repeated failures, only if the available data supports reliable correlation.

## Status

No Cowrie-specific Wazuh detection in this directory is claimed as implemented or validated yet. Add a rule only after confirming the event schema and test procedure. Sanitize all exported rules and evidence before publication.
