# WK1-PM1 VirtualBox and Kali Linux Lab Setup

**Status: Partial progress — the required lab network configuration is not complete.**

This repository records the current state of the VirtualBox/Kali setup. The attached image is a genuine capture of the unlocked Kali desktop; it does not demonstrate the required network configuration.

## Verified so far

- VirtualBox 7.2 and a Kali Linux VM are present.
- The Kali desktop is accessible and internet access was previously observed.
- Bidirectional clipboard and drag-and-drop are enabled in the VM settings.
- A VirtualBox NAT Network with the `10.0.0.0/24` prefix exists.

## Still required

- Connect Kali to that NAT Network instead of ordinary NAT.
- Configure and verify Kali at `10.0.0.2/24` (with the lab gateway/DNS settings from the handout).
- Capture screenshots showing the configured network and connectivity.
- Complete any remaining shared-folder and snapshot steps required by the lab guide.

Kali was observed on ordinary NAT at `10.0.2.15`; therefore this is **not a completed lab submission**. The network configuration was left unchanged because guest administrator authentication was unavailable and the VM had previously experienced a stalled snapshot operation.

## Evidence

![Kali desktop — desktop evidence only, not network configuration](evidence/kali-desktop.jpg)
