# Network setup detail

## How Kali gets 10.0.0.2/24

VirtualBox's NAT Network DHCP server uses `10.0.0.2` by default, which collides with the address the handout requires for Kali. It was moved to `10.0.0.254`, and Kali's NIC was given a fixed DHCP mapping of `10.0.0.2`, so Kali comes up on the required address on every boot without manual reconfiguration.

DHCP layout:

- Server: `10.0.0.254`
- Gateway: `10.0.0.1`
- Pool: `10.0.0.3` - `10.0.0.253` (free for the other VMs in the handout diagram: .7, .9, .10, .11, .16)
- Fixed mapping: Kali NIC MAC `08:00:27:c3:fb:76` -> `10.0.0.2`

## Re-check inside Kali

```
ip -brief address
ip route
nmcli device show eth0
curl -I https://www.kali.org
mount -t vboxsf
ls -la /media/downloads
```

## If internet drops

The handout's fix for VirtualBox 7 / Kali 2026.x:

```
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```
