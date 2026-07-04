# System Security Plan (SSP)

Living description of the homelab environment, its boundary, its controls,
and their implementation state. Maintained across all program projects.
Modeled loosely on the NIST SP 800-18 SSP structure, scaled for a lab.

Last updated: 2026-07-01

## 1. System Identification

**System Name:** Homelab GRC Program Environment
**System Owner:** A. LaSalle
**Purpose:** Simulated enterprise security environment used to build, test, and
govern a security program from baseline through incident response, producing
portfolio-grade GRC artifacts.

## 2. System Boundary

Virtualized environment on a single Proxmox VE 8.4 host (HP EliteDesk 800 G2).
All internal systems sit behind an OPNsense firewall. WAN uplink to home network;
no inbound exposure from the internet.

## 3. System Components

| Component | Role | Segment (intended) | Notes |
|-----------|------|--------------------|-------|
| OPNsense (VM 101) | Firewall / gateway | Management (native) | Inter-VLAN policy enforcement |
| Kali Linux (VM 100) | Analyst / attack workstation | Management | Asset scanning, future adversary emulation |
| Ubuntu Server (VM 102) | SIEM host (Splunk) | Monitoring (VLAN 20) | Currently on native LAN pending R-001 |
| Windows Server 2022 (VM 103) | AD target / lab system | Lab (VLAN 30) | Currently on native LAN pending R-001 |

## 4. Network Architecture

Three-tier VLAN segmentation designed (Management / Monitoring / Lab) with
least-privilege inter-VLAN firewall rules. See Project 1 network diagram.
VLAN interfaces and DHCP configured on OPNsense; VM-level tagging incomplete
(see Risk Register R-001, Control Catalog C-001).

## 5. Control Implementation Summary

Controls are tracked in the Control Catalog. As of this revision:
- C-002 (Asset Inventory) — Implemented, Tested
- C-003 (Network Data Flow Mapping) — Implemented, Tested
- C-001 (Network Segmentation) — Partial, Not Tested

## 6. Data Classification

Governed by the Data Classification Policy (see /policies). Classification
levels and handling requirements defined therein.

## Change Log

- 2026-07-01 — SSP initialized during Project 1. Documents current environment,
  boundary, and control state. Notes R-001 segmentation gap affecting intended
  VM placement.
