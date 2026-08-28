# `flyoverhead.openwrt`

[![Version](https://img.shields.io/badge/version-2.0.0-blue)](galaxy.yml)
[![ansible-core](https://img.shields.io/badge/ansible--core-%E2%89%A52.16-black?logo=ansible&logoColor=white)](https://docs.ansible.com/ansible-core/devel/index.html)
[![License](https://img.shields.io/badge/license-GPL--3.0--only-green)](https://www.gnu.org/licenses/gpl-3.0)
[![Platform](https://img.shields.io/badge/platform-OpenWrt%2022.03%20%7C%2023.05-00B5E2?logo=openwrt&logoColor=white)](#-supported-os)
[![Roles](https://img.shields.io/badge/roles-12-orange)](#-roles)

Configuration of OpenWrt devices over SSH, without Python on the target. Every
role drives UCI directly: system, network, wireless, mesh, firewall, DHCP/DNS,
Wireguard, policy-based routing, SSH and a Prometheus exporter.

These roles configure a device that already runs OpenWrt. Flashing the firmware
is out of scope.

## 🚀 Quick Start

### Requirements

- `ansible-core >=2.16` on the controller

- Collections: `ansible.utils >=2.5.0` (the `wireguard` role uses `ipmath`)

- The [`gekmihesg.openwrt`](https://github.com/gekmihesg/ansible-openwrt) role.
  It supplies the `uci` and `opkg` modules and the action-plugin monkeypatch
  that rewrites `ansible.builtin.*` calls into shell equivalents. Every role
  here depends on it, and it is resolved through `roles_path`, not as a
  collection.

- Task `>=3.20`, for the helper targets only

- SSH access to the device as `root`

### Installation

Installing the dependencies:

```bash
ansible-galaxy collection install -r requirements.yml
ansible-galaxy role install -r requirements.yml -p .ansible/roles
pip install -r requirements.txt
```

Or, equivalently, `task install`.

Installing the collection itself:

```bash
ansible-galaxy collection install git+https://github.com/flyoverhead/ansible-openwrt.git
```

### Roles usage

Full documentation and usage examples of role `<role>` can be found in
`roles/<role>/README.md`.

Order matters. Run `extroot` first — it migrates the overlay onto USB and
reboots, so anything committed before it lands on the overlay that is about to
be replaced. `system` and `network` come next, since later roles attach to the
interfaces `network` defines. `batman` or `mesh11sd` must precede `wireless`,
because both create wireless interfaces of their own. Everything after that is
independent.

### Example Playbook

```yaml
---
- name: configure openwrt devices
  hosts: openwrt
  ignore_unreachable: true
  gather_facts: false

  roles:
    - flyoverhead.openwrt.extroot
    - flyoverhead.openwrt.system
    - flyoverhead.openwrt.network
    - flyoverhead.openwrt.batman
    - flyoverhead.openwrt.wireless
    - flyoverhead.openwrt.wireguard
    - flyoverhead.openwrt.firewall
    - flyoverhead.openwrt.pbr
    - flyoverhead.openwrt.dropbear
    - flyoverhead.openwrt.dhcp
    - flyoverhead.openwrt.node_exporter
```

`gather_facts` must be `false`: fact gathering needs Python, which these devices
do not have.

### Example Variables

A complete, working configuration for two devices lives under
[`tests/`](tests/) and is the reference this collection is developed against:

| File | Contents |
| :--- | :--- |
| [`tests/group_vars/openwrt.yml`](tests/group_vars/openwrt.yml) | Everything shared: network, wireless, mesh, DHCP, Wireguard, firewall, PBR, dropbear |
| [`tests/host_vars/archer.yml`](tests/host_vars/archer.yml) | Per-device settings for a TP-Link Archer C7 |
| [`tests/host_vars/mikrotik.yml`](tests/host_vars/mikrotik.yml) | Per-device settings for a MikroTik hAP ac² |

That configuration:

- Enables extroot on an external USB device
- Creates an IoT network isolated from LAN, and disables the WAN IPv6 interface
- Builds a B.A.T.M.A.N. mesh, separating LAN and IoT over VLAN ports `bat0.2`
  and `bat0.3`
- Replaces the stock APs with LAN (5 GHz) and IoT (2.4 and 5 GHz) APs, with
  802.11r fast BSS transition
- Configures dnsmasq, DHCP pools and static leases
- Creates one Wireguard interface for inbound remote access and one for routing
  out through a VPS
- Sets firewall zones, forwardings, rules and redirects
- Routes selected domains through the VPS with policy-based routing

## 🖥 Supported OS

| OS | Status |
| :--- | :--- |
| OpenWrt 23.05 | Supported |
| OpenWrt 22.03 | Supported |
| OpenWrt 24.10 and newer | **Not supported** — see Gotchas |

Tested on:

- [MikroTik RouterBOARD hAP ac²](https://openwrt.org/toh/mikrotik/hap_ac2)
- [TP-Link Archer C7 AC1750](https://openwrt.org/toh/tp-link/archer_c7)
- [Xiaomi Mi Router 4A Gigabit Edition](https://openwrt.org/inbox/toh/xiaomi/xiaomi_mi_router_4a_gigabit_edition)

## 📦 Roles

| Name | Description |
| :--- | :--- |
| [`batman`](roles/batman/README.md) | B.A.T.M.A.N. adv mesh interfaces and mesh-capable wpad |
| [`dhcp`](roles/dhcp/README.md) | dnsmasq options, DHCP pools and static leases |
| [`dropbear`](roles/dropbear/README.md) | Dropbear SSH daemon settings and authorized keys |
| [`extroot`](roles/extroot/README.md) | External root on USB, with overlay migration and restore |
| [`firewall`](roles/firewall/README.md) | Defaults, zones, forwardings, rules, redirects, ipsets, NAT |
| [`mesh11sd`](roles/mesh11sd/README.md) | 802.11s mesh interfaces and the mesh11sd daemon |
| [`network`](roles/network/README.md) | Globals, devices, interfaces, rules and routes |
| [`node_exporter`](roles/node_exporter/README.md) | Prometheus node-exporter-lua and its listen settings |
| [`pbr`](roles/pbr/README.md) | Policy-Based Routing service settings and policies |
| [`system`](roles/system/README.md) | Hostname, description, timezone and logging |
| [`wireguard`](roles/wireguard/README.md) | Wireguard interfaces and peers, with key generation |
| [`wireless`](roles/wireless/README.md) | Radios and interfaces, including 802.11r fast roaming |

## ⚠️ Gotchas

- **The host group must be named `openwrt`.** The `gekmihesg.openwrt` vars
  plugin only rewrites `ansible.builtin.*` to its shell modules for hosts in a
  group with that exact name. Outside it, Ansible sends real Python modules to a
  device that has no Python and every task fails. See
  [gekmihesg/ansible-openwrt](https://github.com/gekmihesg/ansible-openwrt#ansible-role-openwrt).

- **OpenWrt 24.10 replaced `opkg` with `apk`.** Every role installs packages
  through `opkg`, and `gekmihesg.openwrt` ships no `apk` wrapper, so package
  installation fails on 24.10 and newer. The UCI configuration tasks themselves
  are unaffected.

- **`extroot` repartitions and reboots.** With `extroot_enabled: true` and no
  extroot yet configured, the role runs `parted` and `mkfs.ext4` over the whole
  of `extroot_device` (`sda` by default), copies the overlay onto it and reboots
  the device. Everything on that disk is destroyed. The default is `false`.

- **`batman` and `mesh11sd` remove wpad packages.** Both uninstall every variant
  listed in `*_non_mesh_pkgs` before installing `wpad-mesh-wolfssl`. On a device
  reached over Wi-Fi, that drops the connection.

- **`pbr` may replace dnsmasq with a snapshot build.** If the installed
  `dnsmasq-full` is older than `pbr_dnsmasq_full_required_version` and the
  release feed has nothing newer, the role removes dnsmasq and installs
  `dnsmasq-full`, `libubox` and `libubus` from the OpenWrt *snapshot* feed —
  mixing snapshot packages into a release install.

- **`extroot` fetches two scripts from the OpenWrt wiki at run time** and
  executes them, so what runs is whatever the wiki serves that day.

- **Every role is a no-op until configured.** All list and dictionary variables
  default to empty, so a role with no configuration installs its packages (where
  it has any) and changes nothing else.

## 🧪 Testing

Devices are defined in [`tests/inventory.yml`](tests/inventory.yml), and their
variables in `tests/group_vars/` and `tests/host_vars/`.

```bash
task provision   # run tests/playbook.yml against the inventory
task lint        # pre-commit over the whole collection
task build       # build the collection tarball
```

There is no VM harness: OpenWrt roles need real hardware, so `provision` runs
against whatever `tests/inventory.yml` points at. The playbook reboots every
device when it finishes.

## 📄 License

[GPL-3.0-only](https://www.gnu.org/licenses/gpl-3.0.txt)

`roles/batman/files/luci-proto-batman-adv.ipk` is a prebuilt OpenWrt package
redistributed here, under its own upstream licence.

## 👤 Author Information

fLy0v3rH34d

## TODO

- ~~[802.11s Based Wireless Mesh Networking](https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s)~~
- ~~[B.A.T.M.A.N.](https://openwrt.org/docs/guide-user/network/wifi/mesh/batman)~~
- [DDNS client](https://openwrt.org/docs/guide-user/services/ddns/client)
- ~~[ExtRoot](https://openwrt.org/docs/guide-user/additional-software/extroot_configuration)~~
- [LED configuration](https://openwrt.org/docs/guide-user/base-system/led_configuration)
- ~~[Prometheus node-exporter](https://www.cloudrocket.at/posts/monitor-openwrt-nodes-with-prometheus/)~~
- ~~[Policy-Based Routing](https://docs.openwrt.melmac.net/pbr/)~~
- [`apk` support for OpenWrt 24.10](https://openwrt.org/docs/guide-user/additional-software/opkg_to_apk)
- [Upgrade firmware](https://openwrt.org/docs/guide-user/installation/sysupgrade.cli)
- ~~[Wireguard](https://openwrt.org/docs/guide-user/services/vpn/wireguard/start)~~
- [Xray VPN](https://openwrt.org/packages/pkgdata/xray-core)
