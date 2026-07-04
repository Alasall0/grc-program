# Data Classification Policy

**Program:** Homelab GRC Program
**Owner:** A. LaSalle
**Version:** 1.0
**Effective Date:** 2026-07-01
**Review Cadence:** Updated as new data types are introduced by subsequent projects.

---

## 1. Purpose

This policy defines how data generated and stored within the homelab environment
is classified, handled, and — where applicable — declassified for public
publication. It exists to ensure that the practice of publishing portfolio
artifacts to public repositories does not expose data that would materially
aid an attacker or compromise the environment.

The policy governs a real, recurring operational decision: the environment
deliberately publishes sanitized artifacts to public GitHub repositories for
portfolio purposes. Every such publication is a declassification event and
must be governed accordingly.

## 2. Scope

Applies to all data produced, processed, or stored within the homelab
environment, including configuration data, security tooling output,
governance documentation, and published portfolio artifacts.

## 3. Classification Levels

Data is classified by the **impact of unauthorized exposure**, not by its
format or origin. A key principle of this policy is that *data describing the
security posture of the environment can be as sensitive as credentials
themselves* — a document listing known weaknesses or detection coverage gaps
provides an attacker with a direct roadmap.

| Level | Definition | Example Data Types | Handling Requirement |
|-------|-----------|--------------------|----------------------|
| **Restricted** | Exposure enables direct compromise or reveals exploitable weaknesses | Credentials and secrets; Wi-Fi keys; raw vulnerability scan output; detection rule logic (SPL/queries) | Never leaves the host. Never published in any form, sanitized or otherwise, where a safe representation does not exist. |
| **Confidential** | Exposure aids attacker reconnaissance | OPNsense configuration; firewall rulesets; full-detail network diagram (real IPs, gap annotations) | Internal use only. Publishable only after sanitization per Section 5. |
| **Internal** | Useful operational context; low individual harm | Asset inventory; service lists; general configuration documentation | Internal by default. Publishable on a case-by-case basis after review. |
| **Public** | Sanitized or governance-layer data intended for external sharing | Published repositories; sanitized network diagrams; READMEs; policies; the Risk Register | Freely shareable. Must pass the sanitization checklist (Section 5) before publication. |

## 4. Declassification Strategy by Data Type

Not all sensitive data is declassified the same way. The correct strategy
depends on whether a safe representation of the data can exist. Three
strategies apply:

**Publish Real (governance-layer data).**
Where data is authored at the governance layer rather than the technical
exploit layer, it may be published as-is. The **Risk Register** is the primary
example: it describes risks at the level of "segmentation control partially
implemented," not at the level of exploitable technical detail. It is safe to
publish because it was written at the correct altitude — this is a property of
good register authorship, not of redaction. Fabricating a synthetic version of
data that already exists in safe form would replace real evidence with fake
evidence for no security benefit, and is prohibited.

**Sanitize Real (redactable technical data).**
Where real data carries genuine value but also genuine risk, and redaction can
make it safe, publish the sanitized real version. **Vulnerability scan output**
is the primary example: the value of vulnerability management is that *real*
findings were discovered and triaged, so fabricating it hollows out the work.
Publish real findings with hostnames and IP addresses genericized and any
still-live exploitable specifics withheld. This mirrors the standard practice
of publishing redacted penetration test reports.

**Demonstrate with Faux (non-redactable technical data).**
Where any real version aids an attacker and no safe governance-layer
representation exists, demonstrate the capability using fabricated but
realistic examples. **Detection logic** is the primary example: publishing
production detection rules hands an attacker a map of what is and is not
monitored. Publish the methodology and a representative example instead of the
deployed rule (e.g., "failed-authentication brute-force detection using a
threshold over a rolling window, mapped to MITRE ATT&CK T1110"), never the
production query.

## 5. Sanitization Checklist (Declassification Control)

This checklist is the operational control governing any transition from
Restricted or Confidential to Public. No data may be published to a public
repository until it has passed every applicable item.

Before publishing any artifact, confirm the following are removed or
transformed:

- [ ] Live credentials, passwords, API keys, tokens, or secrets of any kind
- [ ] Wi-Fi keys or pre-shared keys
- [ ] Real WAN IP addresses and any externally routable addressing
- [ ] Internal IP addressing replaced with generic/example ranges where the real values add no instructional value
- [ ] Unsanitized vulnerability details tied to still-live, unpatched systems
- [ ] Production detection rule logic (replaced with methodology + representative example)
- [ ] Gap annotations on network diagrams that reveal exploitable weaknesses
- [ ] Any hostname, identifier, or path that maps directly to a live system in a way that aids targeting

If an artifact cannot pass this checklist even after transformation, it remains
Restricted and is not published in any form.

## 6. Roles and Responsibilities

As a single-operator environment, the system owner holds all roles. In an
enterprise context these would separate into data owner (classifies),
data custodian (enforces handling), and a reviewer/approver (authorizes
declassification). This policy is authored to map cleanly onto that separation
so that the process, not the person, is the control.

## 7. Policy Maintenance

This policy is a living document. Each subsequent project that introduces a new
data type must classify it here before that data is generated at scale. The
change log below records revisions.

## Change Log

- 2026-07-01 — v1.0 established during Project 1 (Network Security Baseline).
  Defines four classification levels, three-way declassification strategy, and
  the sanitization checklist as the governing declassification control. Maps
  current homelab data types (credentials, OPNsense config, asset inventory,
  network diagram, risk register) to classification levels.
