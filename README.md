# WK1-PM1 - Cybersecurity Lab: VirtualBox + Kali Linux

**Status: complete** - all handout requirements configured and verified (VirtualBox 7.2.22, Kali 2026.3).

- NAT Network `NatNetwork` on 10.0.0.0/24, gateway `10.0.0.1`
- Kali `eth0`: `10.0.0.2/24`, full internet verified (HTTP 200)
- Clipboard and drag/drop: bidirectional
- Shared folder: host `Downloads\downloads` -> `/media/downloads` (rw, auto-mount)
- Snapshot: `WK1-PM1-lab-complete`

Evidence: `evidence/kali-network-verification.txt` (live guest output)
Desktop image: `evidence/kali-desktop.jpg`
Setup detail: `docs/network-setup.md`
