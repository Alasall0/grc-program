# System Security Plan (SSP)

Living description of the homelab environment, its boundary, its controls,
and their implementation state. Maintained across all program projects.
Modeled loosely on the NIST SP 800-18 SSP structure, scaled for a lab.

Last updated: 2026-07-12

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

| Component | Role | Segment | IP Address | Notes |
|-----------|------|---------|------------|-------|
| OPNsense (VM 101) | Firewall / gateway | All (trunk) | 10.10.10.1 / 10.10.20.1 / 10.10.30.1 | Inter-VLAN policy enforcement. LAN NIC configured as a trunk carrying VLANs 20 and 30 tagged, with Management as the native untagged VLAN. |
| Kali Linux (VM 100) | Analyst / attack workstation | Management (native) | 10.10.10.117 | Asset scanning, future adversary emulation |
| Ubuntu Server (VM 102) | SIEM host (Splunk) | Monitoring (VLAN 20) | 10.10.20.169 | Splunk deployment pending (Project 2) |
| Windows Server 2022 (VM 103) | AD target / lab system | Lab (VLAN 30) | 10.10.30.120 | AD promotion pending |

## 4. Network Architecture

Three-tier VLAN segmentation is implemented and verified. Least-privilege
inter-VLAN firewall rules enforce a default-deny posture between segments.

- **Management (native, untagged)** - 10.10.10.0/24. Administrative access and
  the analyst workstation. Permitted to reach all other segments.
- **Monitoring (VLAN 20, tagged)** - 10.10.20.0/24. SIEM and log aggregation.
  Egress restricted to DNS and NTP only; cannot initiate connections to other
  internal segments (SIEM pivot defense).
- **Lab / Vulnerable (VLAN 30, tagged)** - 10.10.30.0/24. Intentionally
  vulnerable systems used as attack targets. Permitted to reach Monitoring only
  on TCP/9997 for log forwarding, and permitted internet egress. Explicitly
  blocked from reaching the Management segment (lateral movement prevention).

The OPNsense LAN interface operates as an 802.1Q trunk carrying VLANs 20 and 30
as tagged, with Management traffic on the native untagged VLAN. See Risk R-002
for the accepted hardening tradeoff associated with using the native VLAN for
management traffic.

Control effectiveness was verified through positive and negative testing of all
inter-segment flows. See Control Catalog C-001.

## 5. Control Implementation Summary

Controls are tracked in the Control Catalog. As of this revision:

- C-001 (Network Segmentation) - Implemented, Tested
- C-002 (Asset Inventory) - Implemented, Tested
- C-003 (Network Data Flow Mapping) - Implemented, Tested
  
## 6. Data Classification

Governed by the Data Classification Policy (see /policies). Classification
levels and handling requirements defined therein.

## Change Log

- 2026-07-12 - SSP updated following completion of VM-level VLAN segmentation. All hosts are now on their intended segments and the interim native-LAN placement noted in the previous revision no longer applies. C-001 updated to Implemented and Tested. Added R-002 reference documenting the accepted hardening tradeoff of running management traffic on the native VLAN.
