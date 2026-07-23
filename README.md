# 2026 Password Spray Campaign — Technical Findings

**Author:** Kevin Kirby, Cybersecurity Specialist
**Observation window:** April – July 2026
**Purpose:** Awareness & collaboration

Sanitized write-up of a password spray campaign observed across Microsoft cloud
authentication telemetry: recurring account targeting, repeated non-success outcomes,
a "5x5" spray pattern, and high-level observables useful for awareness and collaboration.

This activity was independently identified and documented during the observation window,
roughly two months before it surfaced in public reporting.

📄 **[Download the full write-up (PDF)](2026-password-spray-campaign.pdf)**

---

## At a glance

| Metric | Value |
| --- | --- |
| Failed logons in activity log | 1,500+ |
| Accounts in activity log | <100 |
| Unique source IPs | 1,400+ |
| Successful compromises identified | **Zero** |

---

## Sanitization notice

This is an external-facing version. The following are intentionally withheld:

- Exact user identifiers
- Exact source IP addresses
- JA4 / TLS fingerprints
- Detection logic and tuning specifics

JA4 signatures are available to responders on request. Detection guidance is described
generically rather than as copy-paste queries, both because schemas differ between
environments and to avoid disclosing detection methods.

---

## Observed activity

During the observation period, recurring authentication activity was observed against a
consistent population of enterprise accounts. The activity was characterized by repeated
non-successful authentication outcomes and repeated attention toward the same account
population.

The clearest pattern was not a single source, provider, or geography. The most useful
observation was a recurring account set paired with repeated failed authentication behavior.

No evidence of successful account compromise was identified within the reviewed telemetry.

> **Primary observation** — The account population appeared more stable than the surrounding
> source details. The 5x5 pattern is a concise way to describe repeated account-focused burst
> behavior observed in the telemetry.

---

## Observed spray pattern (5x5)

The 5x5 pattern is an observational shorthand for a repeated spray shape seen in the reviewed
authentication activity. It should not be interpreted as a strict mathematical rule or a
one-to-one count of password guesses. The same burst shape was later reflected in public
reporting on this activity, reinforcing that it is a meaningful characteristic of the campaign
rather than a local artifact.

The important observation is the shape of activity:

- Small account-level bursts (~5 attempts)
- Source variation around the burst (~5 source details)
- Movement across additional accounts
- Repeated non-success outcomes

---

## Summary of observed activity

| Observed area | Summary |
| --- | --- |
| Account targeting | Repeated activity was observed against a recurring population of enterprise accounts. |
| Authentication path | Azure CLI activity was repeatedly observed using the ROPC (resource-owner password credentials) grant, alongside legacy WS-Trust (`OrgIdWsTrust2`) authentication calls. |
| Outcome pattern | Reviewed events were dominated by non-success authentication outcomes, including account-lockout and invalid-credential conditions. |
| 5x5 pattern | Small bursts, source variation, and movement across the recurring account population shaped the observed spray pattern. |
| Compromise evidence | No successful account compromise was identified in the reviewed telemetry. |

---

## Observed indicators and artifacts

Indicators are kept at a high level for awareness and collaboration.

| Category | Observed indicator / artifact | Use in this report |
| --- | --- | --- |
| Authentication app | Microsoft Azure CLI | Recurring authentication context. |
| Authentication operation | ROPC token grant (OAuth2) and legacy WS-Trust (`OrgIdWsTrust2`) auth | Repeated authentication path observed in cloud logs. |
| Resource target | Azure Resource Manager | Repeated target resource in reviewed authentication telemetry. |
| Client presentation | Rich Client 1.2.7 | Recurring client presentation in Azure CLI-focused activity. |
| Logon context | Interactive user sign-ins; single-factor requirement recorded | Authentication context observed in reviewed logs. |
| Failure outcomes | Account-lockout and invalid-credential validation events | Primary non-success outcome pattern. |
| Activity-log presentation | Microsoft 365, PC, Windows 10 / Chrome 120 family | Consistent presentation in activity-log records. |
| Source/provider context | Frantech (AS53667), Hurricane Electric (AS6939), OVH (AS16276), LSHIY / Forhosting, Alibaba Cloud HK (AS45102), Fast Servers, 3xK Tech, and other observed providers | Context only; not treated as the primary analytical finding. |
| TLS / JA4 artifacts | Withheld from this summary | Kept out of the external-facing version to avoid exposing low-level fingerprints. |

> **Note on ASNs:** hosting and provider infrastructure ages quickly. These are published as
> context for correlation, not as durable blocklist material.

---

## MITRE ATT&CK mapping

Reflects observed or assessed behavior in the reviewed telemetry. Intentionally concise;
avoids operational detection details.

| MITRE ID | Technique | How it maps |
| --- | --- | --- |
| [T1110.003](https://attack.mitre.org/techniques/T1110/003/) | Password Spraying | Repeated failed authentication activity across a recurring account population. |
| [T1078.004](https://attack.mitre.org/techniques/T1078/004/) | Valid Accounts: Cloud Accounts | Activity focused on cloud account authentication; no successful compromise identified. |
| [T1589.002](https://attack.mitre.org/techniques/T1589/002/) | Gather Victim Identity Information: Email Addresses | Recurring account population suggests the account list was known before or during the activity; source not established. |
| [T1583.003](https://attack.mitre.org/techniques/T1583/003/) | Acquire Infrastructure: Virtual Private Server | Authentication activity observed from multiple hosting and provider contexts. |
| [T1036](https://attack.mitre.org/techniques/T1036/) | Masquerading | Client and user-agent presentation appeared as ordinary software or browser-style activity in portions of the telemetry. |
| [T1087.004](https://attack.mitre.org/techniques/T1087/004/) | Account Discovery: Cloud Account | Repeated attention to a defined account population may be consistent with cloud account discovery or validation behavior. |

---

## Account-lockout and invalid-credential activity

A significant portion of the reviewed activity consisted of account-lockout messages and
invalid-credential validation messages.

Repeated locked-account events indicate continued authentication activity against accounts
that were already in a locked state. **These events should not be interpreted as a one-to-one
count of unique password guesses.**

The lockout-heavy activity is operationally relevant because it can create user-facing access
issues and recurring authentication noise even without evidence of successful compromise.

---

## Conclusion

The reviewed telemetry showed recurring authentication activity against a comparatively stable
account population. The 5x5 pattern provides a concise way to describe the observed
account-focused burst behavior.

The most important observations were recurring account targeting, repeated non-success
authentication outcomes, and Azure CLI ROPC and legacy WS-Trust authentication context.

No evidence of successful account compromise was identified within the reviewed data.

---

## Contact

If you are seeing similar auth-flow abuse in your tenant, I'm glad to compare notes.
JA4 signatures are available to responders on request — open an issue or reach out directly.

---

## License

This work is licensed under [CC BY 4.0](LICENSE). Attribution appreciated if you reference
the findings or infrastructure list.
