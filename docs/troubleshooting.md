# Troubleshooting: Kali has no internet on a VirtualBox NAT Network

## Symptom

Kali on a NAT Network (10.0.0.0/24) comes up with no IPv4 address at all. Inside the guest:

```
eth0  connecting (getting IP configuration)
```

NetworkManager then gives up with `ip-config-unavailable`, and `eth0` only has a link-local IPv6 address. `curl` fails with `Could not resolve host`.

## Why it happens

VirtualBox's NAT Network DHCP server uses **10.0.0.2** by default - the same address this lab requires for Kali. Two things then break DHCP:

1. The DHCP server itself owns 10.0.0.2, so it cannot hand that address to a client.
2. If both a MAC-level and a VM-NIC-level fixed address were added for the same adapter, the entries conflict and no lease is offered.

The guest is fine; the host-side DHCP configuration is the problem.

## Fix (host side, no guest password needed)

Run these with the VM powered off. Set the path to `VBoxManage.exe` for your system.

1. Check the current DHCP configuration and look for `Individual Config` entries:

```
VBoxManage list dhcpservers
```

2. Move the DHCP server off 10.0.0.2 and leave .2 out of the pool:

```
VBoxManage dhcpserver modify --network=NatNetwork --server-ip=10.0.0.254 --lower-ip=10.0.0.3 --upper-ip=10.0.0.253
```

3. Remove any duplicate per-client entries, then add exactly one:

```
VBoxManage dhcpserver modify --network=NatNetwork --mac-address=<KALI_MAC> --remove-config
VBoxManage dhcpserver modify --network=NatNetwork --vm=<VM_NAME_OR_UUID> --nic=1 --remove-config
VBoxManage dhcpserver modify --network=NatNetwork --vm=<VM_NAME_OR_UUID> --nic=1 --fixed-address=10.0.0.2
```

4. Restart the NAT Network so the change is applied:

```
VBoxManage natnetwork stop --netname NatNetwork
VBoxManage natnetwork start --netname NatNetwork
```

5. Boot Kali and verify from inside the guest:

```
ip -brief address
ip route
curl -I https://www.kali.org
```

You want `eth0: 10.0.0.2/24`, a default route via `10.0.0.1`, and `HTTP 200`.

## Gotchas

- `dhcpserver start` may report `Cannot start DHCP server because it is already running`. That is harmless.
- `dhcpserver findlease` can keep showing an old expired `10.0.0.3` record even after the guest is on `.2`. Trust `ip -brief address` inside Kali instead of the lease table.
- Never put the DHCP server on `10.0.0.2` and reserve `10.0.0.2` for a client at the same time.
- Keep the pool off `.2` (`--lower-ip=10.0.0.3`) so the reserved address can never be handed to another VM.

## Kali-side fix (the handout's own workaround)

If Kali has an IP but DNS or browsing still fails:

```
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

This needs the guest administrator password.

## Why a DHCP reservation instead of a static IP in Kali

Setting a manual address in Kali means editing the saved NetworkManager profile, which requires root. From a non-interactive session polkit refuses with `Not authorized to control networking`. A fixed DHCP mapping gives the same required `10.0.0.2/24` on every boot without needing guest administrator rights.
