# Control Catalog

Living record of security controls across the homelab GRC program.
Each control is proposed, implemented, tested, and tracked to a status.
Controls map to NIST CSF 2.0 subcategories and are referenced by the
Risk Register where they treat identified risks.

Last updated: 2026-07-01

| ID | Control Name | Description | CSF 2.0 Subcategory | Implementation Status | Test Status | Related Risk | Owner |
|----|--------------|-------------|---------------------|----------------------|-------------|--------------|-------|
| C-001 | Network Segmentation (VLAN) | Three-tier VLAN segmentation (Management / Monitoring / Lab) enforced via OPNsense inter-VLAN firewall rules with least-privilege default-deny. | PR.IR-01 | Implemented | Tested | R-001 (closed), R-002 (accepted) | A. LaSalle |
| C-002 | Asset Inventory | Automated network asset discovery producing a risk-tiered asset register. | ID.AM-01, ID.AM-02 | Implemented | Tested | — | A. LaSalle |
| C-003 | Network Data Flow Mapping | Documented network architecture diagram showing segments, IP ranges, and inter-segment traffic policy. | ID.AM-03 | Implemented | Tested | — | A. LaSalle |

## Status Definitions

**Implementation Status:** Proposed / Partial / Implemented
**Test Status:** Not Tested / Tested / Failed

## Change Log

- 2026-07-01 — C-001 through C-003 established during Project 1. C-001 marked Partial: VLAN architecture and firewall rules configured in OPNsense, but VM-level tagging incomplete (see R-001). C-002 and C-003 fully implemented and verified.
- 2026-07-12 - C-001 updated to Implemented and Tested. VM-level VLAN tagging completed following the R-001 trunk fix. All hosts are now on their intended segments: Kali on Management (native, 10.10.10.117), Ubuntu on Monitoring (VLAN 20, 10.10.20.169), Windows Server on Lab (VLAN 30, 10.10.30.120). Control effectiveness verified through a seven-test matrix covering both permitted and denied flows:

  | Test | Expected | Result |
  |------|----------|--------|
  | Management to OPNsense gateway | Pass | Pass |
  | Management to Monitoring | Pass | Pass (TTL 63, confirming inter-VLAN routing) |
  | Management to Lab | Pass | OPNsense passed the traffic (verified in firewall log); Windows host firewall dropped it at the host layer, as designed |
  | Lab to Management host | Block | Blocked |
  | Lab to firewall interface | Block | Blocked |
  | Monitoring to Lab | Block | Blocked |
  | Monitoring to Management | Block | Blocked |

  Note on the Management-to-Lab result: the OPNsense firewall log confirmed a PASS action on the LAB interface, demonstrating that the network-layer control permitted the traffic as designed. The connection was then dropped by the Windows host firewall. This is defense in depth functioning correctly across two independent control layers, not a control failure.
