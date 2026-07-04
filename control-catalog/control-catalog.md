# Control Catalog

Living record of security controls across the homelab GRC program.
Each control is proposed, implemented, tested, and tracked to a status.
Controls map to NIST CSF 2.0 subcategories and are referenced by the
Risk Register where they treat identified risks.

Last updated: 2026-07-01

| ID | Control Name | Description | CSF 2.0 Subcategory | Implementation Status | Test Status | Related Risk | Owner |
|----|--------------|-------------|---------------------|----------------------|-------------|--------------|-------|
| C-001 | Network Segmentation (VLAN) | Three-tier VLAN segmentation (Management / Monitoring / Lab) enforced via OPNsense inter-VLAN firewall rules with least-privilege default-deny. | PR.IR-01 | Partial | Not Tested | R-001 | A. LaSalle |
| C-002 | Asset Inventory | Automated network asset discovery producing a risk-tiered asset register. | ID.AM-01, ID.AM-02 | Implemented | Tested | — | A. LaSalle |
| C-003 | Network Data Flow Mapping | Documented network architecture diagram showing segments, IP ranges, and inter-segment traffic policy. | ID.AM-03 | Implemented | Tested | — | A. LaSalle |

## Status Definitions

**Implementation Status:** Proposed / Partial / Implemented
**Test Status:** Not Tested / Tested / Failed

## Change Log

- 2026-07-01 — C-001 through C-003 established during Project 1. C-001 marked Partial: VLAN architecture and firewall rules configured in OPNsense, but VM-level tagging incomplete (see R-001). C-002 and C-003 fully implemented and verified.
