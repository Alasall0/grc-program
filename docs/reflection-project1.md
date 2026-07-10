# Reflection Note - Project 1: Network Security Baseline & Asset Inventory

**Author:** A. LaSalle
**Date:** 2026-07-01
**Project:** Network Security Baseline & Asset Inventory

---

## Purpose

This note captures what I learned building Project 1, both the technical lessons and the process lessons. It is written honestly, including the parts that did not go as planned, because tracking what actually happened is more useful than recording a clean version that leaves out the problems.

## Technical Lesson: The VLAN Tagging Failure (R-001)

The most valuable technical lesson from this project came from a failure, not a success. After configuring three-tier VLAN segmentation in OPNsense and enabling VLAN awareness on the Proxmox bridge, the Windows Server in the Lab segment could not pull a DHCP lease on its VLAN. Instead of guessing, I worked the problem in layers.

First I confirmed the network layer was fine, since ICMP still worked. Then I checked whether the DHCP request was even reaching the firewall by reading the Dnsmasq logs, which showed the last activity for that host was on the untagged parent interface, not the tagged VLAN. To confirm where the frames were actually going, I ran a packet capture on the OPNsense VLAN interface while forcing the Windows host to renew its lease. Zero packets arrived. That told me the frames were not making it from the Proxmox bridge to the firewall, even though the bridge configuration showed the correct VLAN tag.

I found that enabling the per-VM firewall in Proxmox inserts an extra bridge that does not handle VLAN tags cleanly, which was part of the problem. Disabling it fixed the bridge-level tagging, confirmed by checking the bridge VLAN table directly. But the packet capture still showed nothing arriving at the firewall, which means there is a second issue further down the path, most likely related to how the virtual NIC driver handles tagged frames. Rather than continue troubleshooting and risk destabilizing a working lab, or sink disproportionate time into a single component, I rolled the change back to a known-good state and documented the issue as Risk R-001 with the diagnostic evidence attached.

The lesson here is not really about VLANs. It is about how to troubleshoot properly. Working the problem layer by layer, using logs and packet captures as evidence instead of guessing, and knowing when to stop and document rather than force a fix, is the same discipline that applies to any security or infrastructure problem. A clean writeup of an unresolved issue, with the evidence that narrows down the cause, is more valuable than pretending everything worked.

## Process Lesson: Administrative Actions Have Side Effects

Earlier in the project, a routine root password reset on OPNsense unexpectedly broke the web interface. The reset restored some default settings, which left the web server bound only to IPv6 and disrupted the firewall ruleset. The network was up and the firewall was running, but the management interface was unreachable. I recovered it by rolling back to a snapshot.

The lesson is that a high-privilege administrative action, even something as routine as a password reset, can have unintended effects on adjacent controls. In a production environment this is exactly the kind of change that a change management process is meant to catch before it happens. It reinforced two habits: take a snapshot before any significant change so there is always a recovery path, and treat "simple" administrative actions with the same caution as complex ones, because their side effects are not always obvious.

## Process Lesson: Scope Discipline

This project expanded in a few places beyond its original definition. Most of that expansion was deliberate and improved the deliverables, like building out the data classification policy into a more complete document than originally planned. But it taught me that scope needs an active control, not just good intentions. Going forward, each project will start with a fixed scope contract and use a parking lot for good ideas that belong in a later project, so that expansion is always a conscious decision rather than accidental drift.

## Networking and Motivation

This project was self-directed. I built it out of my own drive to learn the field and grow my technical depth, so that I can hold more substantive conversations with GRC practitioners rather than staying at a surface level. I have started building my professional network in this space, and the networking and the hands-on work reinforce each other. The more I build, the more technical the conversations I can have, and those conversations will feed back into later projects.

## Summary

Project 1 delivered a verified asset inventory, a segmented network architecture with least-privilege firewall rules, a network diagram, and the governance documents that sit on top of them. It also produced one honestly tracked open risk and a clear set of lessons about troubleshooting, change management, and scope. The most important takeaway is that documenting reality, including what is unfinished, is worth more than presenting a version where nothing ever went wrong.
