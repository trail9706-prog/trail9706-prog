# My SOC Home Lab in AWS with Wazuh — All Commands

This is my **"Build a SOC Home Lab in AWS — Detect Real Attacks with Wazuh (No PC Needed) Project"**.

Copy them in order. Two things you must fill in yourself:

- `<SERVER-PRIVATE-IP>` — your Wazuh server's **private** IP (find it in the EC2 console, under the server instance's "Private IPv4 address"). Using the wrong value here causes the `1208` enrollment error.
- `<CHOOSE-A-PASSWORD>` — a throwaway password for the demo account. It lives on a machine you'll terminate, so anything works.

> Tip: sync the clock on **every** machine you launch (`sudo timedatectl set-ntp true`), or your alerts won't line up on the dashboard. Give the server a 50GB disk — a full disk silently stops the dashboard from receiving alerts.

---

## Part 6 — Install Wazuh (on the SERVER, via Instance Connect)

Install Wazuh (all-in-one). Confirm the version it installs and note it:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

Sync the clock (run this on the server AND both agents):

```bash
sudo timedatectl set-ntp true
```

---

## Part 8 — Connect the endpoints

### Linux (Ubuntu) agent — via Instance Connect

Use the deploy command the dashboard generates (Agents → Deploy new agent) — it embeds the server's private IP for you. After it runs and you've started the agent, confirm it connected:

```bash
sudo grep -E "Connected to|Unable to connect|Valid key" /var/ossec/logs/ossec.log | tail -10
```

Check the server sees the agent (run this on the SERVER):

```bash
sudo /var/ossec/bin/agent_control -l
```

### Windows agent — in PowerShell as Administrator

Install and enroll (replace `<SERVER-PRIVATE-IP>` with your server's private IP):

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.7-1.msi -OutFile $env:tmp\wazuh-agent; msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='<SERVER-PRIVATE-IP>' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='windows-endpoint'
```

Start the agent:

```powershell
NET START Wazuh
```

---

## Attack 1 — Brute force (on the UBUNTU agent)

Fire 20 failed SSH logins:

```bash
for i in $(seq 1 20); do timeout 3 ssh wronguser@127.0.0.1 -o StrictHostKeyChecking=no -o BatchMode=yes -o PreferredAuthentications=password -o PubkeyAuthentication=no 2>/dev/null; done
```

**Dashboard** → Threat Hunting → ubuntu-endpoint → search box:

```
rule.groups:authentication_failed OR rule.groups:authentication_failures
```

You'll see the individual level-5 failures and the single level-10 brute-force alert (MITRE T1110).

---

## Attack 2 — Hidden admin account (on the WINDOWS agent, PowerShell as Admin)

Create the account, then add it to administrators:

```powershell
net user hsupport <CHOOSE-A-PASSWORD> /add
net localgroup administrators hsupport /add
```

**Dashboard** → Threat Hunting → windows-endpoint → search box:

```
rule.groups:account_changed
```

You'll see the account creation (level 8) and the admin-group addition (level 12), tagged "Account Manipulation".

*If you see nothing:* your Windows image may need account auditing enabled. Run once, then re-test:

```powershell
auditpol /set /subcategory:"User Account Management" /success:enable /failure:enable
auditpol /set /subcategory:"Security Group Management" /success:enable /failure:enable
```

---

## Attack 3 — Log wipe (on the WINDOWS agent, PowerShell as Admin)

Clear the security log:

```powershell
wevtutil cl Security
```

**Dashboard** → Threat Hunting → windows-endpoint → search box:

```
rule.groups:log_clearing_auditlog
```

You'll see "The audit log was cleared" (Windows event 1102, MITRE T1070).

---

## One dashboard, three attacks

Clear the agent filter (all agents), set the time range to the last hour, and search:

```
rule.groups:authentication_failures or rule.groups:account_changed or rule.groups:windows_security or rule.groups:log_clearing_auditlog
```

Add `agent.name` and `rule.level` as columns, then sort by `rule.level` descending.

---

## Cleanup

Remove the demo account (Windows, PowerShell as Admin):

```powershell
net user hsupport /delete
```

Then terminate all instances in the AWS console when you're done — that stops all charges.

---

Thanks for reviewing!
