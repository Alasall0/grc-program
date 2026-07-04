# Risk Register

Living record of identified risks across the homelab GRC program.
Maintained continuously — entries are added, updated, and closed as the
program matures. Status changes are preserved in git history.

Last updated: 2026-07-01

| ID | Date Opened | Risk Description | Likelihood | Impact | Inherent Risk | Treatment | Control Ref | Residual Risk | Status | Owner |
|----|-------------|------------------|------------|--------|---------------|-----------|-------------|---------------|--------|-------|
| R-001 | 2026-07-01 | VM VLAN tagging fails to pass tagged frames from Proxmox bridge to OPNsense; Windows Server (Lab segment) cannot obtain DHCP on VLAN 30. Network segmentation control designed but not fully implemented. | Medium | Medium | Medium | Mitigate | C-001 | TBD | Open | A. LaSalle |

## Scoring Key

**Likelihood:** Low / Medium / High
**Impact:** Low / Medium / High
**Inherent Risk:** risk level before controls applied
**Residual Risk:** risk level remaining after controls applied
**Treatment:** Mitigate / Accept / Transfer / Avoid

## Change Log

- 2026-07-01 — R-001 opened. VLAN tagging failure identified during Project 1 network segmentation implementation. Root cause hypothesis: VirtIO VLAN offload or vtnet parent driver handling of tagged frames. Diagnostic evidence: `bridge vlan show` confirms tap103i0 tagged VLAN 30; tcpdump on OPNsense vlan03 shows zero DHCP frames arriving. Interim state: Lab VM on native LAN. Remediation pending dedicated troubleshooting session.
