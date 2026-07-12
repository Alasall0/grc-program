# Risk Register

Living record of identified risks across the homelab GRC program.
Maintained continuously — entries are added, updated, and closed as the
program matures. Status changes are preserved in git history.

Last updated: 2026-07-12

| ID | Date Opened | Risk Description | Likelihood | Impact | Inherent Risk | Treatment | Control Ref | Residual Risk | Status | Owner |
|----|-------------|------------------|------------|--------|---------------|-----------|-------------|---------------|--------|-------|
| R-001 | 2026-07-01 | VM VLAN tagging fails to pass tagged frames from Proxmox bridge to OPNsense; Windows Server (Lab segment) cannot obtain DHCP on VLAN 30. Network segmentation control designed but not fully implemented. | Medium | Medium | Medium | Mitigate | C-001 | None | Closed | A. LaSalle |
| R-002 | 2026-07-12 | Management traffic runs on the native (untagged) VLAN. This is a known hardening weakness, as native-VLAN trunks are the historical vector for VLAN hopping via double-tagged frames. | Low | Medium | Medium | Accept | C-001 | Medium | Open (Accepted) | A. LaSalle |## Scoring Key

**Likelihood:** Low / Medium / High
**Impact:** Low / Medium / High
**Inherent Risk:** risk level before controls applied
**Residual Risk:** risk level remaining after controls applied
**Treatment:** Mitigate / Accept / Transfer / Avoid

## Change Log

- 2026-07-01 — R-001 opened. VLAN tagging failure identified during Project 1 network segmentation implementation. Root cause hypothesis: VirtIO VLAN offload or vtnet parent driver handling of tagged frames. Diagnostic evidence: `bridge vlan show` confirms tap103i0 tagged VLAN 30; tcpdump on OPNsense vlan03 shows zero DHCP frames arriving. Interim state: Lab VM on native LAN. Remediation pending dedicated troubleshooting session.

- 2026-07-12 - R-001 CLOSED. Root cause identified: the OPNsense LAN NIC was configured as an access port on the default VLAN, with no membership in VLANs 20 or 30. The Linux bridge therefore had no port to forward tagged frames to, and silently dropped them. Fix: configured the OPNsense LAN NIC as a trunk port carrying VLANs 20 and 30 tagged (Proxmox `trunks=20;30`, a CLI-only option that is not exposed in the web GUI), and disabled the per-VM firewall shim, which is not VLAN-aware. Verified: Windows Server obtained DHCP lease 10.10.30.120 on VLAN 30, and Ubuntu Server obtained 10.10.20.169 on VLAN 20. Residual risk: None.

- 2026-07-12 - R-002 opened and accepted. Management traffic uses the native untagged VLAN rather than a tagged VLAN. This is a recognized hardening weakness because native-VLAN trunks are the traditional vector for VLAN hopping via double-tagged frames. The risk is accepted rather than mitigated: in a single-administrator lab with no out-of-band management path, tagging the management VLAN materially increases the risk of administrative lockout, which was assessed as the greater practical risk. Compensating factors: the environment has no untrusted devices on the trunk, and no external access to the internal bridge. This decision will be revisited if the environment gains additional hosts or external connectivity.
