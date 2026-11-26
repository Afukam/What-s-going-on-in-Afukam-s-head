# Troubleshooting Log — AWS Cowrie + Wazuh

This page records practical obstacles encountered while building the lab. It separates verified outcomes from unresolved items. Host addresses, account IDs, credentials, and sensitive network details are intentionally omitted.

## 1. Cowrie installation and filesystem permissions

**Symptom:** Accessing Cowrie installation paths directly as the Ubuntu account was inconsistent; some paths under the dedicated `cowrie` home directory returned permission denied.

**Investigation:** Compared ownership and permissions of `/home/cowrie`, the Cowrie project directory, and the virtual environment. Used privileged, read-only inspection to distinguish directory traversal restrictions from missing files. Inspected the running process working directory and executable through `/proc`.

**Useful diagnostics:**

```bash
namei -l /home/cowrie/cowrie
sudo readlink -f /proc/<PID>/cwd
sudo readlink -f /proc/<PID>/exe
sudo ls -la /home/cowrie/cowrie
```

**Finding:** The investigation established the Cowrie working directory and Python runtime. It also showed that an expected `bin/cowrie` executable was absent; the running process used the project's virtual-environment Python/Twistd entry point instead. A permission-denied message alone does not prove that a file exists.

## 2. Cowrie service management

**Symptom:** Cowrie was running, but no `cowrie.service` unit was registered with systemd. The process was observed under a transient session scope rather than a persistent named service.

**Investigation:** Checked systemd for a Cowrie unit, inspected the process tree, parent process, command line, working directory, and executable, and verified root write access to `/etc/systemd/system` before attempting service-file administration.

**Learning:** A running process is not necessarily managed by a persistent systemd service. Process state, service registration, and boot persistence are separate things.

**Status:** Do not claim reliable boot-time service management until the unit is enabled, starts cleanly after reboot, and survives a controlled restart.

## 3. Cowrie listener and controlled SSH testing

The configured SSH listener used port 2222 and advertised port 23. A representative configuration was:

```ini
[ssh]
enabled = true
listen_endpoints = tcp:2222:interface=0.0.0.0
reported_port = 23
```

Controlled test command:

```bash
ssh -p 2222 testuser@<HONEYPOT_HOST>
```

**Outcome:** Local and Mac-client test sessions successfully reached Cowrie. JSON logs recorded session and command-input activity. The source address observed during the Mac test was the owner's own test client, not an unknown attacker. The honeypot was not yet receiving unsolicited internet traffic.

## 4. Locating and interpreting Cowrie logs

**Symptom:** The expected log file was not under the Ubuntu user's home directory.

**Finding:** Cowrie JSON events were located under the Cowrie project tree:

```text
/home/cowrie/cowrie/var/log/cowrie/cowrie.json
```

Confirm the application's working directory and configured log path rather than assuming logs are written relative to the interactive user's home directory.

## 5. AWS connectivity and Systems Manager

**Symptom:** Systems Manager reported an error while requesting managed-instance role credentials. SSH access and the intended network path also required iterative checks.

**Work performed:** Reviewed instance state and network configuration, checked subnet and route-table selections, reviewed security-group inbound rules, and added the required rule during the controlled setup. Connectivity was eventually restored sufficiently to continue the lab.

**Status:** The precise final IAM/SSM root cause should be documented only after reviewing the final instance role and SSM agent status. Do not publish account IDs, security-group IDs, public addresses, or credentials.

## 6. Wazuh dashboard access

**Symptom:** Opening `https://localhost:8443` in the browser returned connection refused, even though the Wazuh dashboard was subsequently accessed and an agent was visible as running.

**Learning:** `localhost` refers to the machine running the browser. A local browser URL does not automatically reach a dashboard on a remote AWS instance. Confirm the intended access method, listener, port, and any tunnel or proxy before diagnosing the dashboard as down.

**Verified progress:** Wazuh dashboard access was established and an agent was confirmed running.

**Still to verify:** Which host the agent represents, manager health, and the complete Cowrie JSON ingestion path.

## 7. Current blocker — end-to-end telemetry validation

Seeing an agent in Wazuh is not sufficient to prove Cowrie log ingestion. The next validation sequence is:

1. Confirm which host the Wazuh agent represents.
2. Confirm Cowrie JSON path and read permissions.
3. Configure collection on the host that can actually read the file.
4. Reload the relevant agent configuration safely.
5. Generate a new controlled SSH session.
6. Search Wazuh for the new event and compare timestamp/content with Cowrie's source JSON.
7. Save sanitized screenshots and sample events.

Only after this should the project claim successful Cowrie-to-Wazuh integration.

## Lessons

- Read the exact error before changing configuration.
- Check ownership, permissions, process state, and service state independently.
- Use process inspection to understand how an application was launched.
- Distinguish a local browser address from a remote service address.
- Validate telemetry end to end with a newly generated event.
- Record failed attempts and the evidence that led to a fix.
- Keep the honeypot isolated until monitoring, access controls, and cost controls are understood.
