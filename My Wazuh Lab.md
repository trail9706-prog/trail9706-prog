<!--
Note: HTML comments don't show when the README renders. Items in [BRACKETS] are for you to fill in
(only your reflection and the troubleshooting story, which I can't know).
-->

# SOC Home Lab in AWS with Wazuh

**Author:** Katrail Harris  
**Email / LinkedIn:** www.linkedin.com/in/only1katrailharris  
**Date completed:** 10/14/2026  
**Time to complete:** 1 hour  
**Platform / Tools:** AWS (EC2), Wazuh 4.14.8, Windows Server 2022, Ubuntu 24.04 LTS, Remmina (RDP), ISO/IEC 27001:2022, MITRE ATT&CK  
**Original project source (if guided):** [https://www.youtube.com/watch?v=KVt3OmdUv3c](https://www.youtube.com/watch?v=KVt3OmdUv3c)

---

![Project overview image](images/00-overview.png)

## Introducing Today's Project!

### Project overview

In this project, I built a small security operations center (SOC) in AWS. A Wazuh server collects security logs from a Windows Server and an Ubuntu endpoint, and I generated suspicious activity on them to see what Wazuh detects and how an analyst would investigate it.

### Tools and concepts

- **Amazon EC2:** Hosts the Wazuh server and the two monitored endpoints in the cloud.
- **Wazuh (Indexer, Manager, Dashboard):** An open-source SIEM/XDR platform. The Indexer stores events, the Manager analyzes them against detection rules, and the Dashboard is where an analyst searches and investigates alerts.
- **Wazuh agents:** Small programs on each endpoint that ship security logs to the server.
- **Remmina:** The Remote Desktop client I used from Linux Mint to log in to the Windows endpoint.

**Key concepts:** SIEM and centralized logging, endpoint agents, authentication monitoring, privileged-account monitoring, audit-log tampering, alert investigation and filtering.

### Project reflection

This project took about 1 hour. [Most challenging part: be specific, e.g. "getting the Windows agent to show as active."] [Most rewarding part: e.g. "seeing the account changes appear in the dashboard within moments of running the commands."]

---

## Step 1: Build the Lab Environment

### What I did in this step

I set up the lab in AWS region us-east-2: one Wazuh server plus a Windows Server 2022 endpoint and an Ubuntu 24.04 endpoint to monitor.

### Understanding a SIEM

A SIEM is like a security camera system for your computers. The cameras (agents) sit on each endpoint, and the control room (the Wazuh server) collects every feed in one place so an analyst can review it instead of walking from machine to machine.

### My configuration

The Wazuh server runs the Indexer, Manager, and Dashboard together. Each endpoint runs an agent that ships its logs to the Manager. Windows is reached by RDP and Ubuntu by SSH.

![Lab architecture](images/00-overview.png)

---

## Step 2: Connect to the Windows Endpoint and Enroll the Agents

### What I did in this step

I logged in to the Windows endpoint over RDP from my Linux Mint computer using Remmina, then confirmed both agents were enrolled and reporting to Wazuh.

### Understanding agent enrollment

Enrollment is the handshake that links an endpoint to the Wazuh Manager. Until an agent shows as **active**, nothing from that machine appears in the dashboard.

### My configuration

I connected to the Windows Server as `Administrator` using the password retrieved from AWS (shown masked below, and the EC2 hostname is redacted). In the Wazuh Endpoints view, both agents show **Active (2)**: `ubuntu-endpoint` (001, Ubuntu 24.04 LTS) and `windows-endpoint` (002, Windows Server 2022 Datacenter), both on Wazuh 4.14.8.

![RDP login](images/01-rdp-login.png)

![Agents active](images/02-agents-active.png)

---

## Step 3: Generate Authentication Events

### What I did in this step

I generated failed and successful logins so I could see how authentication activity shows up in Wazuh.

### Understanding authentication monitoring

Many failed logins in a short time can indicate someone guessing passwords. Wazuh counts authentication failures and successes so an analyst can spot that pattern.

### My configuration

Over the last 24 hours the dashboard showed 978 total events, **20 authentication failures**, and **6 authentication successes**. These are observed event counts, not 20 separate attackers.

![Authentication dashboard](images/03-authentication-dashboard.png)

---

## Step 4: Create a Windows Account and Change Group Membership

### What I did in this step

On the Windows endpoint, I created a temporary local account and changed local group membership, then searched for the resulting events in Wazuh. I did this only in my isolated lab.

### Understanding privileged-account changes

Attackers who get into a system often create a new account or add one to the Administrators group so they can keep access later. Monitoring group changes is how defenders notice this.

### My configuration

I used an Events search across the authentication, account-changed, and Windows security rule groups. It returned 6 hits for the Windows agent, including:

| Rule description | Rule ID | Level |
|---|---|---|
| Administrators Group Changed | 60154 | 12 |
| Users Group Changed | 60170 | 5 |
| User account changed | 60110 | 8 |
| User account enabled or created | 60109 | 8 |
| Domain Users Group Changed | 60160 | 5 |

The Administrators group change was the highest-severity event (level 12), which matches the risk.

![Account manipulation events](images/04-account-manipulation.png)

---

## Step 5: Clear the Windows Security Log

### What I did in this step

I cleared the Windows Security event log from the command line on the Windows endpoint, then searched Wazuh to see whether it recorded that.

### Understanding audit-log clearing

Clearing the security log is a common way to cover tracks after an intrusion, so defenders treat it as a red flag.

### My configuration

I filtered the Windows agent on the `log_clearing_auditlog` rule group. It returned **1 event**. The dashboard for that view showed 0 alerts at level 12 or above.

![Log clearing search](images/05-log-clearing.png)

---

## Testing and Verification

### How I tested it

I ran each action on the Windows endpoint (failed and successful logins, account and group changes, Security log clearing) and then used the Wazuh Dashboard and Events views, filtering by agent and rule group, to confirm each one produced events.

### What went wrong and how I fixed it

[Optional but valuable: describe one real problem you hit, how you investigated it, and the root cause. If nothing went wrong, delete this section.]

---

## ISO/IEC 27001:2022 Control Mapping

This lab shows how technical monitoring supports logging, monitoring, and access-control requirements in ISO/IEC 27001:2022 Annex A. The mapping is illustrative and does not claim compliance.

| Component I built | Annex A control | What it demonstrates | Audit evidence to request |
|---|---|---|---|
| Wazuh agents shipping logs to a central server | 8.15 Logging | Security events are recorded and collected centrally | Agent inventory, log source list, retention settings |
| Wazuh dashboards, alerts, and filtered searches | 8.16 Monitoring activities | Systems are watched for anomalous behavior | Alert rules, sample alerts, review records |
| Authentication failure/success counts | 8.5 Secure authentication | Failed and successful logins can be reviewed | Authentication event exports, lockout settings |
| Administrators group change alert (rule 60154) | 8.2 Privileged access rights, 5.18 Access rights | Changes to privileged groups are detected | Admin group membership list, change tickets for alerts |
| Log-clearing search | 8.15 Logging (protection of log information) | Tampering with audit logs can be detected | Evidence logs are forwarded off the endpoint, alert follow-up records |

**Not covered by this lab:** Alert notifications to a person, a documented incident response procedure, log retention and backup, MFA, restricting Wazuh and RDP access to trusted IPs, access reviews, and time synchronization.

## Sample Audit Finding (Illustrative)

This finding is based on a design choice in this lab, not a real client.

**Finding title:** Audit-log clearing not surfaced as a high-priority alert

**Criteria:** ISO/IEC 27001:2022 Annex A 8.15 (Logging) and 8.16 (Monitoring activities)

**Condition:** Clearing the Windows Security log produced one event in the `log_clearing_auditlog` rule group, and the dashboard for that view showed 0 alerts at level 12 or above. The Administrators group change was level 12.

**Cause:** Default rule severity was left unchanged and no notification was configured.

**Effect:** An attacker erasing evidence could go unnoticed unless an analyst happens to search for it.

**Recommendation:**
1. Review the log-clearing rule's severity and raise it if appropriate.
2. Configure an email or chat notification for log-clearing and administrator-group changes.
3. Forward logs off the endpoint so clearing local logs doesn't erase the record.

**Status in this lab:** Open

---

## Security Analyst Notes

**MITRE ATT&CK mapping:**
- T1136.001 Create Account: Local Account
- T1098 Account Manipulation
- T1070.001 Indicator Removal: Clear Windows Event Logs
- T1110 Brute Force (authentication failures observed)

**Detection logic:** Wazuh agents collect Windows Security log events, and the Manager matches them to rules such as 60109 (account created), 60154 (Administrators group changed), and the `log_clearing_auditlog` rule group.

**False positive considerations:** Legitimate administrators create accounts and change groups, and people mistype passwords. Log clearing can also occur during maintenance. Compare against change tickets and the user's normal behavior.

**Triage steps:** Check which agent and account were involved, the timestamp, and the rule level. Ask whether the change was authorized, and look for other events around the same time, such as new logins from the new account.

---

## Skills Demonstrated

- Cloud lab deployment in AWS (EC2)
- SIEM/XDR operation and Wazuh agent enrollment
- Log analysis, filtering, and alert investigation
- Mapping technical findings to ISO/IEC 27001:2022 and MITRE ATT&CK
- Writing an audit-style finding with criteria, condition, cause, effect, and recommendation

## Resources

- [Original guided project (YouTube)](https://www.youtube.com/watch?v=KVt3OmdUv3c)
- [Wazuh documentation](https://documentation.wazuh.com)

---

*Built by Katrail Harris. Based on a guided Wazuh SOC home lab video. Connect with me on [LinkedIn](https://www.linkedin.com/in/only1katrailharris).*
