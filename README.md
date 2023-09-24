# Ansible Collection - flyoverhead.openwrt

Ansible collection for automative configuration of OpenWrt devices (without Python).

---

## Compatibilities

This collection was tested on
- [MikroTik RouterBOARD hAP ac²](https://openwrt.org/toh/mikrotik/hap_ac2)
- [TP-Link Archer C7 AC1750](https://openwrt.org/toh/tp-link/archer_c7)
- [Xiaomi Mi Router 4A Gigabit Edition](https://openwrt.org/inbox/toh/xiaomi/xiaomi_mi_router_4a_gigabit_edition)

## Supported OS

- OpenWrt 22.03

## Installation and Usage

### Requirements

- Ansible `>=2.13`

- Task `>=3.20`

### Installation

Cloning repo:

```bash
git clone https://github.com/flyoverhead/ansible-openwrt
```

Installing requirements:

```bash
cd ansible-openwrt
ansible-galaxy collection install -r requirements.yml
```

### Roles usage

Full documentation and usage examples of role `<role>` can be found in `roles/<role>/README.md`.

### Example Playbook

<details>
  <summary>playbook.yml</summary>

```yaml
---
- name: Configure openwrt
  hosts: openwrt
  ignore_unreachable: true
  gather_facts: false
  tags: configure_openwrt

  pre_tasks:
    - name: Update package cache
      ansible.builtin.command:
        cmd: "opkg update"
      changed_when: false

  roles:
    - extroot
    - system
    - network
    - batman
    - wireless
    - wireguard
    - firewall
    - pbr
    - dropbear
    - dhcp

  post_tasks:
    - name: Reboot device
      ansible.builtin.command:
        cmd: "reboot"
      changed_when: false
```

> For `gekmihesg.openwrt` role works properly it is mandatory to use `openwrt` as a hosts group name. More details can be found at [gekmihesg/ansible-openwrt#ansible-role-openwrt](https://github.com/gekmihesg/ansible-openwrt#ansible-role-openwrt).

</details>

### Example Variables

<details>
  <summary>Description</summary>
<br />
Example configuration for `TP-Link Archer C7` and `MikroTik hAP ac²`

- Enable extroot for external USB device
- Create network for IoT devices (IOT) isolated from LAN
- Disable WAN IPv6 network interface
- Create B.A.T.M.A.N. mesh network interfaces
- Separate mesh networks (LAN and IOT) by mapping VLAN ports (bat0.2 and bat0.3)
- Delete default WIFI APs
- Create WIFI mesh interface for mesh nodes (server) communication
- Create WIFI APs for LAN (5GHz only) and IOT (both 2.4Ghz and 5GHz) networks with fast BSS transition (802.11r) support
- Configure Dnsmasq and DHCP with static leases for example LAN and IOT clients
- Create Wireguard interface for remote access to the router from the Internet with peers
- Create Wireguard interface for access to remote VPS server (for VPN routing)
- Configure firewall zones, zone forwardings, rules and redirects
- Configure Policy-Based routing for example domains to remote VPS server
</details>

<br />
<b>host_vars</b>
<details>
  <summary>archer.yml</summary>

```yaml
# Device subnet
device_ip_address: "192.168.1.1"
device_dhcp_start: "50"

# Device LAN bridge port
device_bridge_port: "eth0.1"

# Device WIFI radios
device_5g_radio: "radio1"
device_2g_radio: "radio0"

# Wireguard Home port
wireguard_home_port: "51820"

# Wireguard VPS address
wireguard_vps_address: "10.0.0.2"

# Enable extroot
extroot_enabled: true

# Configure system settings
system:
  hostname: "archer"
  description: "TP-Link Archer C7 AC1750"
  timezone: "UTC"
  zonename: "UTC"
```
</details>

<details>
  <summary>mikrotik.yml</summary>

```yaml
# Device subnet
device_ip_address: "192.168.1.2"
device_dhcp_start: "100"

# Device LAN bridge port
device_bridge_port: "eth0"

# Device WIFI radios
device_5g_radio: "radio1"
device_2g_radio: "radio0"

# Wireguard Home port
wireguard_home_port: "51821"

# Wireguard VPS address
wireguard_vps_address: "10.0.0.3"

# Enable extroot
extroot_enabled: true

# Configure system settings
system:
  hostname: "mikrotik"
  description: "MikroTik hAP ac2"
  timezone: "UTC"
  zonename: "UTC"
```
</details>

<br />
<b>group_vars</b>
<details>
  <summary>openwrt.yml</summary>

```yaml
# Configure WIFI
wifi_password: "passowrd"

# Configure network devices
network_devices:
  - id: "@device[0]"
    name: "br-lan"
    state: "present"
    type: "bridge"
    ports: ["{{ device_bridge_port }}", "bat0.2"]
    stp: "1"
    igmp_snooping: "1"
    ipv6: "0"
  - id: "iot_dev"
    name: "br-iot"
    state: "present"
    type: "bridge"
    ports: ["bat0.3"]
    stp: "1"
    igmp_snooping: "1"
    ipv6: "0"

# Configure network interfaces
network_interfaces:
  - id: "lan"
    state: "present"
    device: "br-lan"
    proto: "static"
    auto: "1"
    force_link: "1"
    ipaddr: "{{ device_ip_address }}"
    netmask: "255.255.255.0"
    mtu: "1536"
    ipv6: "0"
    delegate: "0"
  - id: "wan"
    state: "present"
    proto: "dhcp"
    auto: "1"
    force_link: "1"
    peerdns: "0"
    dns: ["192.168.1.2", "192.168.1.3"]
    ipv6: "0"
    delegate: "0"
  - id: "iot"
    state: "present"
    device: "br-iot"
    proto: "static"
    auto: "1"
    force_link: "1"
    ipaddr: "{{ device_ip_address | ansible.utils.ipmath(2560) }}"
    netmask: "255.255.255.0"
    mtu: "1536"
    ipv6: "0"
    delegate: "0"
  - id: "wan6"
    state: "absent"

# Configure batman mesh network
batman_enabled: true
ath10k_ct_fix: true

batman_network_interfaces:
  - id: "bat0"
    state: "present"
    proto: "batadv"
    routing_algo: "BATMAN_IV"
    fragmentation: "1"
    gw_mode: "server"
    bridge_loop_avoidance: "1"
    distributed_arp_table: "1"
    multicast_mode: "1"
    hop_penalty: "30"
    delegate: "0"
  - id: "batmesh"
    state: "present"
    proto: "batadv_hardif"
    master: "bat0"
    mtu: "2304"
    delegate: "0"

batman_wireless_interfaces:
  - id: "mesh"
    name: "mesh"
    state: "present"
    device: "{{ device_5g_radio }}"
    network: ["batmesh"]
    mode: "mesh"
    mesh_id: "mesh"
    mesh_fwding: "0"
    encryption: "sae"
    key: "{{ wifi_password }}"
    disabled: "0"

# Configure wireless network
wireless_devices:
  - id: "{{ device_5g_radio }}"
    type: "mac80211"
    channel: "44"
    htmode: "VHT80"
    disabled: "0"
  - id: "{{ device_2g_radio }}"
    type: "mac80211"
    channel: "1"
    htmode: "VHT40"
    disabled: "0"

wireless_interfaces:
  - id: "lan5"
    name: "wlan5"
    state: "present"
    device: "{{ device_5g_radio }}"
    network: ["lan"]
    mode: "ap"
    ssid: "lan5"
    encryption: "sae-mixed"
    key: "{{ wifi_password }}"
    ieee80211r: "1"
    mobility_domain: "1"
    ft_over_ds: "1"
    ft_psk_generate_local: "1"
    disabled: "0"
  - id: "iot5"
    name: "wiot5"
    state: "present"
    device: "{{ device_5g_radio }}"
    network: ["iot"]
    mode: "ap"
    ssid: "iot5"
    encryption: "sae-mixed"
    key: "{{ wifi_password }}"
    ieee80211r: "1"
    mobility_domain: "1"
    ft_over_ds: "1"
    ft_psk_generate_local: "1"
    disabled: "0"
  - id: "iot2"
    name: "wiot2"
    state: "present"
    device: "{{ device_2g_radio }}"
    network: ["iot"]
    mode: "ap"
    ssid: "iot2"
    encryption: "sae-mixed"
    key: "{{ wifi_password }}"
    ieee80211r: "1"
    mobility_domain: "1"
    ft_over_ds: "1"
    ft_psk_generate_local: "1"
    disabled: "0"
  - id: "default_radio0"
    state: "absent"
  - id: "default_radio1"
    state: "absent"

# Configure dnsmasq and dhcp
dhcp_common:
  authoritative: "1"
  boguspriv: "1"
  cachesize: "1000"
  domainneeded: "1"
  dnssec: "1"
  dnsseccheckunsigned: "1"
  filterwin2k: "1"
  rebind_protection: "1"
  rebind_localhost: "1"
  server: ["1.1.1.1", "9.9.9.9"]
  allservers: "1"
  localservice: "0"
  nonegcache: "1"

dhcp_pools:
  - interface: "lan"
    state: "present"
    force: "1"
    dhcpv4: "server"
    limit: "50"
    start: "{{ device_dhcp_start }}"
    ra: "disabled"
    dhcpv6: "disabled"
    dns_service: "0"
  - interface: "iot"
    state: "present"
    force: "1"
    dhcpv4: "server"
    limit: "50"
    start: "{{ device_dhcp_start }}"
    ra: "disabled"
    dhcpv6: "disabled"
    dns_service: "0"

dhcp_leases:
  - id: "host01"
    name: "host01"
    state: "present"
    mac: "00:11:22:33:44:55"
  - id: "host02"
    name: "host02"
    state: "present"
    mac: "55:44:33:22:11:00"

# Configure wireguard interfaces
wireguard_interfaces:
  - id: "wg_home"
    state: "present"
    proto: "wireguard"
    addresses: "{{ device_ip_address | ansible.utils.ipmath(5120) }}"
    listen_port: "{{ wireguard_home_port }}"
    peers: ["peer01", "peer02"]
  - id: "wg_remote"
    state: "present"
    proto: "wireguard"
    addresses: "{{ wireguard_vps_address }}"

# Configure wireguard peers
wireguard_peers:
  - id: "vps"
    name: "VPS"
    state: "present"
    public_key: "{{ wireguard_server_public_key }}"
    preshared_key: "your_preshared_key"
    endpoint_host: "{{ wireguard_server_address }}"
    endpoint_port: "{{ wireguard_server_port }}"
    route_allowed_ips: "0"
    persistent_keepalive: "25"
    allowed_ips: ["0.0.0.0/0"]
    wireguard_interface_name: "wg_remote"

# Configure firewall
firewall_defaults:
  input: "ACCEPT"
  forward: "REJECT"
  output: "ACCEPT"
  drop_invalid: "1"
  synflood_protect: "1"
  flow_offloading: "1"
  flow_offloading_hw: "1"

firewall_zones:
  - id: "@zone[0]"
    name: "lan"
    state: "present"
    network: ["lan", "wg_home"]
    input: "ACCEPT"
    forward: "ACCEPT"
    output: "ACCEPT"
    family: "ipv4"
  - id: "iot"
    name: "iot"
    state: "present"
    network: ["iot"]
    input: "DROP"
    forward: "DROP"
    output: "ACCEPT"
    family: "ipv4"
  - id: "@zone[1]"
    name: "wan"
    state: "present"
    network: ["wan", "wg_remote"]
    masq: "1"
    mtu_fix: "1"
    input: "DROP"
    forward: "DROP"
    output: "ACCEPT"
    family: "ipv4"

firewall_forwardings:
  - id: "lan_iot"
    state: "present"
    src: "lan"
    dest: "iot"
    family: "ipv4"
  - id: "iot_wan"
    state: "present"
    src: "iot"
    dest: "wan"
    family: "ipv4"

firewall_rules:
  - id: "allow_iot_dhcp_dns"
    name: "Allow DHCP and DNS for IoT network"
    state: "present"
    src: "iot"
    dest_port: ["53", "67", "68"]
    target: "ACCEPT"
    family: "ipv4"
  - id: "wg_home_access"
    name: "Allow remote access to Wireguard home server"
    state: "present"
    src: "wan"
    dest_port: ["{{ wireguard_home_port }}"]
    target: "ACCEPT"
    proto: ["udp"]
    family: "ipv4"

firewall_redirects:
  - id: "force_dns_lan"
    name: "Force DNS for LAN network"
    state: "present"
    src: "lan"
    src_dport: "53"
    target: "DNAT"
    family: "ipv4"
  - id: "force_dns_iot"
    name: "Force DNS for IoT network"
    state: "present"
    src: "iot"
    src_dport: "53"
    target: "DNAT"
    family: "ipv4"

# Configure policy-based routing
pbr_service:
  enabled: "1"
  verbosity: "0"
  strict_enforcement: "0"
  resolver_set: "dnsmasq.nftset"
  ipv6_enabled: "0"
  ignored_interface: ["wg_home"]
  boot_timeout: "30"
  rule_create_option: "add"
  webui_show_ignore_target: "0"
  webui_supported_protocol: ["all", "tcp", "udp", "tcp udp", "icmp"]

pbr_policies:
  - id: "instagram"
    name: "Instagram"
    state: "present"
    enabled: "1"
    interface: "wg_remote"
    dest_addr:
      [
        "129.134.0.0/16",
        "179.60.0.0/16",
        "185.60.0.0/16",
        "185.199.0.0/16",
        "31.13.0.0/16",
        "157.240.0.0/16",
        "instagram.com",
      ]
    chain: "prerouting"

# Configure dropbear
dropbear:
  enable: "1"
  verbose: "0"
  PasswordAuth: "1"
  Port: "22"
  RootPasswordAuth: "1"
  RootLogin: "1"
  Interface: "lan"
  keyfile: "/etc/dropbear/authorized_keys"
  mdns: "0"
  MaxAuthTries: "3"
```

</details>

## Included content

### Roles

| Name | Description |
| :--- | :--- |
| [flyoverhead.openwrt.batman](roles/batman/README.md) | Ansible role for OpenWrt `B.A.T.M.A.N.` mesh network configuration |
| [flyoverhead.openwrt.dhcp](roles/dhcp/README.md) | Ansible role for OpenWrt `dhcp` configuration |
| [flyoverhead.openwrt.dropbear](roles/dropbear/README.md) | Ansible role for OpenWrt `dropbear` configuration |
| [flyoverhead.openwrt.extroot](roles/extroot/README.md) | Ansible role for OpenWrt `extroot` configuration |
| [flyoverhead.openwrt.firewall](roles/firewall/README.md) | Ansible role for OpenWrt `firewall` configuration |
| [flyoverhead.openwrt.mesh11sd](roles/mesh11sd/README.md) | Ansible role for OpenWrt `802.11s` mesh network configuration |
| [flyoverhead.openwrt.network](roles/network/README.md) | Ansible role for OpenWrt `network` configuration |
| [flyoverhead.openwrt.pbr](roles/pbr/README.md) | Ansible role for OpenWrt `Policy-Based Routing` configuration |
| [flyoverhead.openwrt.system](roles/system/README.md) | Ansible role for OpenWrt `system` configuration |
| [flyoverhead.openwrt.wireguard](roles/wireguard/README.md) | Ansible role for OpenWrt `wireguard` configuration |
| [flyoverhead.openwrt.wireless](roles/wireless/README.md) | Ansible role for OpenWrt `wireless` configuration |

## Testing

All required variables should be defined in `tests/vars.yml`.

Test can be launched from the root directory of the collection by running:

```Bash
task test
```

## License

- GPL-3.0-only

## Author Information

fLy0v3rH34d

## TODO

- ~~[B.A.T.M.A.N.](https://openwrt.org/docs/guide-user/network/wifi/mesh/batman)~~
- [DDNS client](https://openwrt.org/docs/guide-user/services/ddns/client)
- ~~[ExtRoot](https://openwrt.org/docs/guide-user/additional-software/extroot_configuration)~~
- ~~[802.11s Based Wireless Mesh Networking](https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s)~~
- ~~[Policy-Based Routing](https://docs.openwrt.melmac.net/pbr/)~~
- ~~[Wireguard](https://openwrt.org/docs/guide-user/services/vpn/wireguard/start)~~
- [Xray VPN](https://openwrt.org/packages/pkgdata/xray-core)

---

## If you like the project please feel free to support further development

| Bitcoin | Ethereum |
| :---: | :---: |
| bc1qrc8etpf4a7a50a0mfjyfv6fkzcg0qn3jmv882u | 0x5b872a332C94006F0f8A032D8a5D799E1668bf9e |
| <img src="https://github.com/flyoverhead/docs/blob/main/images/bitcoin.png?raw=true" alt="drawing" width="200"/> | <img src="https://github.com/flyoverhead/docs/blob/main/images/ethereum.png?raw=true" alt="drawing" width="200"/> |