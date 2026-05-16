# Threat Intel & Detection Lab

My practice work applying MITRE ATT&CK to real-world threat campaigns.

Each folder is a self-contained analysis of one adversary campaign, including:
- Report analysis mapped to ATT&CK techniques
- A detection rule (Sigma format) for a key technique
- A hunt playbook for proactively finding the threat

## Analyses

| # | Campaign | Threat Actor | Key Technique |
|---|----------|--------------|---------------|
| 01 | SolarWinds Supply Chain | APT29 | Credential Dumping (T1003.001) |

## Methodology

For each campaign I follow this process:
1. Read the source report
2. Identify adversary behaviors
3. Map each behavior to an ATT&CK tactic and technique
4. Pick the most critical technique and write a detection rule for it
5. Build a hunt playbook around that technique

## Tools

- [MITRE ATT&CK](https://attack.mitre.org/)
- [Sigma Rules](https://sigmahq.io/) for detection
- [ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) for visualization
