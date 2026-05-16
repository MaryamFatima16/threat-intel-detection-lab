# APT29 - SolarWinds Supply Chain Attack

## Source

- Mandiant: Highly Evasive Attacker Leverages SolarWinds Supply Chain
- Microsoft: Analyzing Solorigate
- CISA Alert AA21-008A

## What Happened (Short Version)

APT29 (Russian SVR) hacked into SolarWinds software build system and planted a backdoor called SUNBURST inside a legitimate update. Around 18,000 organizations installed this update. APT29 then picked high-value targets (US government, tech companies), activated the backdoor, stole credentials, read emails, and exfiltrated data.

## Behaviors I Identified and Mapped

| # | What the adversary did | Tactic | Technique ID | Confidence |
|---|------------------------|--------|--------------|------------|
| 1 | Injected backdoor into SolarWinds update | Initial Access | T1195.002 | High |
| 2 | Ran encoded PowerShell after activation | Execution | T1059.001 | High |
| 3 | Created scheduled tasks for persistence | Persistence | T1053.005 | Medium |
| 4 | Named malicious files to match legitimate ones | Defense Evasion | T1036.005 | High |
| 5 | Checked for security tools and stayed dormant | Defense Evasion | T1562.001 | High |
| 6 | Dumped credentials from LSASS memory | Credential Access | T1003.001 | High |
| 7 | Stole SAML signing certificates from ADFS | Credential Access | T1552.004 | High |
| 8 | Moved to other systems via SMB | Lateral Movement | T1021.002 | Medium |
| 9 | Read emails in Microsoft 365 | Collection | T1114.002 | High |
| 10 | Used HTTPS disguised as SolarWinds traffic for C2 | Command and Control | T1071.001 | High |
| 11 | Exfiltrated stolen data over the C2 channel | Exfiltration | T1041 | High |

## Why I Chose T1003.001 for Detection

Credential dumping from LSASS is:
- Used by almost every advanced attacker (not just APT29)
- Rarely done by legitimate users (low false positive)
- A critical pivot point - if you catch it here, you stop lateral movement
- Detectable with standard Sysmon logging

## My Takeaway

This campaign was hard to detect because the initial access came through a trusted vendor update. But once inside, APT29 still had to perform standard post-compromise techniques (credential theft, lateral movement) - and those ARE detectable with behavioral analytics.
