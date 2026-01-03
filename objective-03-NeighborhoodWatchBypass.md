# Objective 3: Neighborhood Watch Bypass

## Challenge Overview

The objective of this challenge was to restore full administrative control of the Dosis Neighborhood Fire Alarm System, which had entered a lockout mode following a suspected compromise.

### At the start of the challenge:
- Administrative privileges had been revoked  
- The system was only partially operational  
- The user had standard, non-root access  
- A restore command was provided but required elevated privileges  

### Mission Goal

Identify a method to bypass the current restrictions, gain administrative (root) privileges, and execute the system restore command:

```
/etc/firealarm/restore_fire_alarm
```

---

## Initial Enumeration

After logging into the system as the standard user `chuser`, initial enumeration was performed to determine the current privilege level.

Commands used included:

```
whoami
id
```

These confirmed that the user had no administrative privileges or special group memberships.

The next step was checking sudo permissions using:

```
sudo -l
```

### Key Finding

The output revealed a critical misconfiguration:

```
(root) NOPASSWD: /usr/local/bin/system_status.sh
```

This meant the user could execute the script `system_status.sh` as root without providing a password, presenting a potential privilege escalation opportunity.

To understand how the allowed script functioned, it was reviewed using:

```
cat /usr/local/bin/system_status.sh
```

The script executed several system monitoring commands, including:

```
free
df
w
ps aux
```

Importantly, these commands were executed **without specifying absolute paths**.

---

## Identifying the Vulnerability

### Vulnerability Type

PATH hijacking via misconfigured sudo script

### Explanation

Linux resolves command execution using the `PATH` environment variable.  
In this environment, the sudo configuration included a `secure_path` that prioritized a user-writable directory before standard system directories.

Because the script used relative command names (such as `df` instead of `/bin/df`), the system would execute a user-controlled binary if it existed earlier in the PATH. This created a privilege escalation vulnerability.

---

## Exploitation Process

A malicious replacement for the `df` command was created in the user’s personal bin directory.By creating a new file called df which contains a script.
```
nano df
```
The script contained:

```
#!/bin/bash
/bin/bash
```

The script simply launched a shell. The file was then made executable.
```
chmod u+x df
```

When the allowed sudo command was executed:

```
sudo /usr/local/bin/system_status.sh
```

The following occurred:

- The script ran with root privileges  
- The system attempted to execute `df`  
- The user-controlled version of `df` was executed first due to PATH ordering  
- A root shell was spawned  

Verification was performed using:

```
whoami
```

The output confirmed root access.

---

## Mission Completion

With root privileges obtained, the final restore command was executed:

```
/etc/firealarm/restore_fire_alarm
```

The command completed successfully with no errors, indicating that full system control had been restored and the challenge objective was met.



