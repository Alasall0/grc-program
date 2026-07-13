# Risk Register

Living record of identified risks across the homelab GRC program.
Maintained continuously — entries are added, updated, and closed as the
program matures. Status changes are preserved in git history.

Last updated: 2026-07-12

| ID | Date Opened | Risk Description | Likelihood | Impact | Inherent Risk | Treatment | Control Ref | Residual Risk | Status | Owner |
|----|-------------|------------------|------------|--------|---------------|-----------|-------------|---------------|--------|-------|
| R-001 | 2026-07-01 | VM VLAN tagging fails to pass tagged frames from Proxmox bridge to OPNsense; Windows Server (Lab segment) cannot obtain DHCP on VLAN 30. Network segmentation control designed but not fully implemented. | Medium | Medium | Medium | Mitigate | C-001 | None | Closed | A. LaSalle |
| R-002 | 2026-07-12 | Management traffic runs on the native (untagged) VLAN. This is a known hardening weakness, as native-VLAN trunks are the historical vector for VLAN hopping via double-tagged frames. | Low | Medium | Medium | Accept | C-001 | Medium | Open (Accepted) | A. LaSalle |## Scoring Key
| R-003 | 2026-07-12 | Temporary firewall exception: outbound TCP/443 permitted from the Monitoring segment to support Splunk installation and package retrieval. While active, this exception weakens the SIEM egress restriction (C-001) by providing a general outbound HTTPS path that could be used for command-and-control or data exfiltration if the host were compromised. | Low | High | Medium | Mitigate | C-001 | None | Closed | A. LaSalle |

**Likelihood:** Low / Medium / High
**Impact:** Low / Medium / High
**Inherent Risk:** risk level before controls applied
**Residual Risk:** risk level remaining after controls applied
**Treatment:** Mitigate / Accept / Transfer / Avoid

## Change Log

- 2026-07-01 — R-001 opened. VLAN tagging failure identified during Project 1 network segmentation implementation. Root cause hypothesis: VirtIO VLAN offload or vtnet parent driver handling of tagged frames. Diagnostic evidence: `bridge vlan show` confirms tap103i0 tagged VLAN 30; tcpdump on OPNsense vlan03 shows zero DHCP frames arriving. Interim state: Lab VM on native LAN. Remediation pending dedicated troubleshooting session.

- 2026-07-12 - R-001 CLOSED. Root cause identified: the OPNsense LAN NIC was configured as an access port on the default VLAN, with no membership in VLANs 20 or 30. The Linux bridge therefore had no port to forward tagged frames to, and silently dropped them. Fix: configured the OPNsense LAN NIC as a trunk port carrying VLANs 20 and 30 tagged (Proxmox `trunks=20;30`, a CLI-only option that is not exposed in the web GUI), and disabled the per-VM firewall shim, which is not VLAN-aware. Verified: Windows Server obtained DHCP lease 10.10.30.120 on VLAN 30, and Ubuntu Server obtained 10.10.20.169 on VLAN 20. Residual risk: None.

- 2026-07-12 - R-002 opened and accepted. Management traffic uses the native untagged VLAN rather than a tagged VLAN. This is a recognized hardening weakness because native-VLAN trunks are the traditional vector for VLAN hopping via double-tagged frames. The risk is accepted rather than mitigated: in a single-administrator lab with no out-of-band management path, tagging the management VLAN materially increases the risk of administrative lockout, which was assessed as the greater practical risk. Compensating factors: the environment has no untrusted devices on the trunk, and no external access to the internal bridge. This decision will be revisited if the environment gains additional hosts or external connectivity.

- 2026-07-12 - R-003 opened. A time-boxed firewall exception was raised to permit outbound TCP/443 from the Monitoring segment. Justification: the Monitoring segment's egress restriction (DNS and NTP only) is functioning as designed and blocks software installation, which prevented Splunk from being downloaded and installed. Rather than permanently weakening the control, a scoped exception was raised for the duration of the installation only. Scope: outbound TCP/443 from Monitoring net. Duration: the Splunk installation session only. Rollback: the rule is to be disabled immediately upon completion of the install, and the control verified as restored. Known follow-on issue: recurring outbound access will be required for operating system and Splunk updates. A permanent solution (destination allowlist to package repositories, or an internal repository mirror) is required and is tracked as a future remediation item.

- 2026-07-12 - R-003 CLOSED. Splunk Enterprise installation completed successfully on the Monitoring segment host (10.10.20.169). The temporary TCP/443 egress rule was disabled immediately upon completion, and the restoration of the control was verified by confirming that outbound HTTPS from the Monitoring segment again fails. The rule was retained in a disabled state rather than deleted, as recurring egress will be required for future updates. Known follow-on issue remains open: a permanent solution for recurring package and Splunk updates is still required, either a destination allowlist scoped to package repositories or an internal repository mirror.
