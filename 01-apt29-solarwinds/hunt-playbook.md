# Hunt Playbook: Credential Dumping via LSASS

## ATT&CK Reference

- **Technique:** T1003.001 - OS Credential Dumping: LSASS Memory
- **Tactic:** Credential Access
- **Used by:** APT29, APT28, FIN7, Lazarus, most ransomware groups

## Hypothesis

If an attacker is inside our network, they will likely try to dump credentials from LSASS memory to get domain passwords for lateral movement.

## What Data Do I Need?

| Log Source | Event | What It Shows |
|------------|-------|---------------|
| Sysmon Event 10 | ProcessAccess | Which process opened LSASS and with what permissions |
| Sysmon Event 1 | Process Creation | Command line of any dumping tool launched |
| Sysmon Event 11 | File Create | Dump files (.dmp) being written to disk |

## How to Hunt

### Step 1 - Look for LSASS access

Search for Sysmon Event 10 where TargetImage = lsass.exe and SourceImage is NOT a known system process.

Splunk example:

    index=sysmon EventCode=10 TargetImage="*lsass.exe"
      NOT SourceImage IN ("*csrss.exe","*wininit.exe","*MsMpEng.exe","*svchost.exe")

### Step 2 - Check what accessed it

For any hits: look at the SourceImage (what process touched LSASS).
- Is it a known tool? (mimikatz, procdump, comsvcs.dll via rundll32)
- Is it something unusual running from a temp folder?
- What user context is it running under?

### Step 3 - Follow the chain

If you find a suspicious hit:
- What was the parent process? (How did the dumper get launched?)
- Were there network connections after the dump? (Credential exfiltration?)
- Did the same user or machine show lateral movement afterward?

## What Does Bad Look Like?

- rundll32.exe accessing LSASS (comsvcs.dll dump method)
- Any .exe from C:\Users\ or C:\Temp\ accessing LSASS
- PowerShell accessing LSASS
- A process accessing LSASS followed by Type 3 logons to other machines within minutes

## What to Do If Found

1. Isolate the host immediately
2. Reset all credentials that were on that machine
3. Check if the attacker already moved laterally (search for those credentials being used elsewhere)
4. Find how the attacker got there in the first place (trace back parent processes)

## Prevention

- Enable Credential Guard (blocks most LSASS dumping on Windows 10+)
- Enable LSA protection (RunAsPPL registry key)
- Restrict debug privileges via Group Policy
