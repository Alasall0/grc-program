# Business Risk Context - Project 1: Network Security Baseline & Asset Inventory

**Program:** Homelab GRC Program
**Author:** A. LaSalle
**Version:** 1.0
**Date:** 2026-07-01
**Audience:** Non-technical stakeholders (leadership, risk owners)

---

## Purpose

This document explains the work of Project 1 in business terms. It covers the business problems the project addresses, what an attacker takes advantage of when these controls are missing, and how a governance team uses the artifacts this project produced. It is written for a reader who does not need the technical detail but does need to understand the risk and the value.

---

## 1. Asset Inventory: You Cannot Secure What You Do Not Know You Have

**The business problem.** An organization is responsible for the security of every asset it operates, but it cannot protect an asset it does not know exists. Unknown and unmanaged assets are where a large share of breaches begin. This includes things like a forgotten test server, a device someone set up and never decommissioned, or shadow IT that was never approved. These are the systems that go unpatched and unmonitored, because no one is aware of them in the first place. The asset that never makes it onto an inventory is often the one sitting exposed with a known vulnerability and no one watching it.

**What this project delivered.** This project built an automated asset discovery capability that scans the network, finds every reachable host, and produces a verified and structured inventory. This inventory is the foundation for nearly every other security activity. Patching, monitoring, incident response, and compliance all depend on the organization first knowing what it actually has.

**Framework mapping.** Maps to NIST CSF 2.0 ID.AM-01 (hardware inventory) and ID.AM-02 (software and services inventory). Tracked as Control C-002 in the program control catalog.

---

## 2. Network Segmentation: Containing the Blast Radius

**The business problem.** On a flat network with no internal segmentation, every system can reach every other system. Because of this, a single compromise can become a network-wide event. If an attacker gains access to one workstation through a phishing email, that workstation and a critical server are only one hop apart. The same initial compromise that should have cost the organization one endpoint can instead expose the entire environment. In risk terms, this means the potential loss from any single incident is much higher, because the impact is network-wide instead of contained.

**What this project delivered.** This project built a three-tier segmented network made up of a Management segment, a Monitoring segment, and a Lab/Vulnerable segment. Least-privilege firewall rules enforce a default-deny posture between these segments, so traffic is only allowed when it is explicitly permitted. High-risk systems are kept isolated from the management plane, and the monitoring layer is protected from being used as a pivot point for further attacks. This limits how far an attacker can move after an initial compromise, and it keeps an incident contained to the segment where it started instead of letting it spread across the whole network.

**Current state.** The segmentation is designed and enforced at the firewall, but one implementation gap remains. Virtual-machine level VLAN tagging is not yet fully working, which is tracked as Risk R-001 and reflected in Control C-001 being marked Partial. In the interim, the affected system sits on the native network segment. This gap is documented, tracked, and scheduled for remediation rather than hidden. This reflects how a mature security program handles a control that is designed but not yet fully in place.

**Framework mapping.** Maps to NIST CSF 2.0 PR.IR-01 (network segmented based on risk). Tracked as Control C-001.

---

## 3. Risk Tiering: Directing Limited Resources to the Right Assets

**The business problem.** No organization has the resources to patch, monitor, and harden every asset at the same level, because security budgets and staff are always limited. A flat, undifferentiated list of assets gives no guidance on where to focus. This leads to spreading defensive effort evenly across systems that do not deserve equal attention, which means a critical system can end up receiving the same treatment as a system that barely matters.

**What this project delivered.** The asset register assigns each host a risk tier of High, Medium, or Low based on its attack surface, which comes down to the services it exposes and the number of open ports it has. This is a preventive assessment made before any incident happens. It lets the organization direct its hardening, patching, and monitoring effort in proportion to each asset's exposure, so the highest-risk systems get attention first. In short, it turns an impossible task of securing everything equally into a realistic plan of securing the highest-exposure assets first.

**An important distinction.** This asset-level risk tiering is about preventive resource allocation, meaning deciding where to invest defensive effort ahead of time. It is not the same as incident triage, which is the reactive prioritization of active incidents during a response. Both use likelihood and impact to make decisions, but one pre-positions defenses before anything happens and the other reacts to events as they occur. This project deals with the preventive side.

---

## 4. How a Governance Team Uses These Artifacts

- The **asset register** becomes the authoritative source of truth that every other security process references. Vulnerability management, monitoring coverage, and incident scoping all start with knowing what assets exist.
- The **network diagram and segmentation policy** provide the documented architecture that a risk team assesses against, and the evidence an auditor reviews when evaluating access controls.
- The **risk tiers** feed directly into resource-allocation decisions and establish the prioritization logic that gets reused in later vulnerability management work.
- The **data classification policy** governs how all of this information is handled and what is safe to share externally.

---

## 5. Residual Risk

After Project 1, the environment has a verified asset inventory, a documented and mostly enforced segmentation architecture, and a working risk-tiering method. The main residual risk is the incomplete VM-level segmentation tracked as R-001, which leaves one high-risk system on the native segment for now. This is tracked and scheduled for remediation. No other significant gaps were identified at the baseline stage, and later projects will assess deeper control layers including monitoring, vulnerability management, and compliance.
