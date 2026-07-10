# Data Classification Policy

**Program:** Homelab GRC Program
**Owner:** A. LaSalle
**Version:** 1.0
**Effective Date:** 2026-07-01
**Review Cadence:** Updated as new data types are introduced by later projects.

---

## 1. Purpose

This policy defines how data created and stored in the homelab environment is classified, handled, and, where appropriate, cleared for public publication. It exists to make sure that publishing portfolio artifacts to public repositories does not expose data that would help an attacker or put the environment at risk.

The policy governs a real and recurring decision. The environment deliberately publishes sanitized artifacts to public GitHub repositories for portfolio purposes, and every one of those publications is a declassification event that needs to be handled with care.

## 2. Scope

This policy applies to all data produced, processed, or stored in the homelab environment. That includes configuration data, security tool output, governance documentation, and published portfolio artifacts.

## 3. Classification Levels

Data is classified by the impact of it being exposed, not by its format or where it came from. A key idea behind this policy is that data describing the security posture of the environment can be just as sensitive as credentials. A document that lists known weaknesses or shows where monitoring has gaps gives an attacker a direct roadmap, and it needs to be protected accordingly.

| Level | Definition | Example Data Types | Handling Requirement |
|-------|-----------|--------------------|----------------------|
| **Restricted** | Exposure allows direct compromise or reveals exploitable weaknesses | Credentials and secrets, Wi-Fi keys, raw vulnerability scan output, detection rule logic (SPL/queries) | Never leaves the host. Never published in any form, sanitized or otherwise, where a safe version cannot exist. |
| **Confidential** | Exposure helps an attacker with reconnaissance | OPNsense configuration, firewall rulesets, full-detail network diagram (real IPs, gap annotations) | Internal use only. Publishable only after sanitization per Section 5. |
| **Internal** | Useful context with low individual harm | Asset inventory, service lists, general configuration documentation | Internal by default. Publishable case by case after review. |
| **Public** | Sanitized or governance-layer data intended for sharing | Published repositories, sanitized network diagrams, READMEs, policies, the Risk Register | Freely shareable. Must pass the sanitization checklist in Section 5 before publication. |

## 4. Declassification Strategy by Data Type

Not all sensitive data is declassified the same way. The right approach depends on whether a safe version of the data can exist at all. Three strategies apply.

**Publish Real (governance-layer data).**
When data is written at the governance layer instead of the technical exploit layer, it can be published as is. The Risk Register is the main example. It describes risks at the level of "segmentation control partially implemented," not at the level of exploitable technical detail. It is safe to publish because it was written at the right altitude, which is a result of good register writing rather than redaction. Fabricating a fake version of data that already exists in a safe form would replace real evidence with fake evidence for no security benefit, so it is not allowed.

**Sanitize Real (redactable technical data).**
When real data carries genuine value but also genuine risk, and redaction can make it safe, publish the sanitized real version. Vulnerability scan output is the main example. The value of vulnerability management is that real findings were discovered and triaged, so faking it would hollow out the work. Publish real findings with hostnames and IP addresses genericized and any still-live exploitable details held back. This matches the standard practice of publishing redacted penetration test reports.

**Demonstrate with Faux (non-redactable technical data).**
When any real version would help an attacker and no safe governance-layer version exists, demonstrate the capability using fabricated but realistic examples. Detection logic is the main example. Publishing production detection rules would hand an attacker a map of what is and is not being monitored. Publish the methodology and a representative example instead of the deployed rule, for example "failed-authentication brute-force detection using a threshold over a rolling window, mapped to MITRE ATT&CK T1110," and never the production query itself.

## 5. Sanitization Checklist (Declassification Control)

This checklist is the actual control that governs any move from Restricted or Confidential to Public. No data may be published to a public repository until it has passed every item that applies to it.

Before publishing any artifact, confirm the following are removed or transformed:

- [ ] Live credentials, passwords, API keys, tokens, or secrets of any kind
- [ ] Wi-Fi keys or pre-shared keys
- [ ] Real WAN IP addresses and any externally routable addressing
- [ ] Internal IP addressing replaced with generic or example ranges where the real values add no instructional value
- [ ] Unsanitized vulnerability details tied to still-live, unpatched systems
- [ ] Production detection rule logic, replaced with methodology and a representative example
- [ ] Gap annotations on network diagrams that reveal exploitable weaknesses
- [ ] Any hostname, identifier, or path that maps directly to a live system in a way that helps an attacker target it

If an artifact cannot pass this checklist even after being transformed, it stays Restricted and is not published in any form.

## 6. Roles and Responsibilities

This is a single-operator environment, so the system owner holds every role. In an enterprise this would separate into a data owner who classifies the data, a data custodian who enforces handling, and a reviewer or approver who authorizes declassification. This policy is written to map cleanly onto that separation, so that the process is the control rather than the person. That way the same policy would still work if these roles were split across a real team.

## 7. Policy Maintenance

This is a living document. Each later project that introduces a new data type has to classify it here before that data is generated at scale. The change log below records revisions.

## Change Log

- 2026-07-01 - v1.0 established during Project 1 (Network Security Baseline). Defines four classification levels, the three-way declassification strategy, and the sanitization checklist as the governing declassification control. Maps the current homelab data types (credentials, OPNsense config, asset inventory, network diagram, risk register) to classification levels.
