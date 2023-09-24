# `flyoverhead.openwrt.network`

OpenWRT `network` configuration
- create/delete/configure network devices
- create/delete/configure network interfaces

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `network_globals` | [Global network configuration options](https://openwrt.org/docs/guide-user/base-system/basic-networking#global_section) |  | `dictionary` |  |
| &emsp;`network_ula_prefix` | IPv6 ULA prefix (`IPv6-prefix` or `auto`) | `optional` | `string` | `auto` |
| &emsp;`network_packet_steering` | Enable or disable usage of every CPU to handle packet traffic | `optional` | `boolean` | `0` |
| `network_devices` | List of [network devices](https://openwrt.org/docs/guide-user/base-system/basic-networking#device_sections) |  | `list of dictionaries` |  |
| &emsp;`id` | Unique device ID | `required` | `string` | `guest_dev` |
| &emsp;`name` | Unique device name | `mandatory` | `string` | `br-guest` |
| &emsp;`state` | Device status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`type` | Device type (for bridge devices only) | `optional` | `string` | `bridge` |
| &emsp;`ports` | List of ports associated with bridge device | `required` | `list` | `["eth0", "eth1"]` |
| &emsp;`stp` | Enable the Spanning Tree Protocol (STP) which prevents network loops | `optional` | `boolean` | `0` |
| &emsp;`igmp_snooping` | Enable IGMP snooping | `optional` | `boolean` | `0` |
| &emsp;`ipv6` | Enable IPv6 support | `optional` | `boolean` | `0` |
| `network_interfaces` | List of [network interfaces](https://openwrt.org/docs/guide-user/base-system/| &emsp;`igmp_snooping` | Enable IGMP snooping | `optional` | `boolean` | `0` |
| &emsp;`id` | Unique interface ID | `mandatory` | `string` | `guest` |
| &emsp;`state` | Interface status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`device` | Network device associated with the interface (for newly created interface) | `optional` | `string` | `guest_dev` |
| &emsp;`proto` | Interface protocol | `mandatory` | `string` | `static` |
| &emsp;`auto` | Specifies whether to bring up interface on boot | `optional` | `boolean` | `0` for proto `none`, else `1` |
| &emsp;`force_link` | Specifies whether ip address, route, and optionally gateway are assigned to the interface regardless of the link being active ('1') or only after the link has become active ('0') | `optional` | `boolean` | `1` for protocol `static`, else `0` |
| &emsp;`ipaddr` | IP address | `required` | `string` | `192.168.1.1` |
| &emsp;`netmask` | Netmask | `required` | `string` | `255.255.255.0` |
| &emsp;`gateway` | Default gateway | `optional` | `string` | `192.168.1.1` |
| &emsp;`peerdns` | Use peer/DHCP provided DNS server(s) | `optional` | `boolean` | `1` |
| &emsp;`dns` | Override peer/DHCP provided DNS server(s) | `optional` | `list` | `["1.1.1.1", "8.8.8.8"]` |
| &emsp;`username` | Username for PAP/CHAP authentication | `optional` | `string` | `username` |
| &emsp;`password` | Password for PAP/CHAP authentication | `optional` | `string` | `password` |
| &emsp;`mtu` | MTU value | `optional` | `integer` | `1532` |
| &emsp;`ipv6` | Enable IPv6 support | `optional` | `boolean` | `0` |
| &emsp;`delegate` | Enable prefix delegation | `optional` | `boolean` | `0` |
| &emsp;`ip6assign` | Assign a part of given length of every public IPv6-prefix to this interface | `optional` | `integer` | `60` |
| `network_rules` | List of [network rules](https://openwrt.org/docs/guide-user/network/routing/ip_rules) |  | (`list of dictionaries`) |  |
| &emsp;`id` | Unique rule ID | `mandatory` | `string` | `rule_example` |
| &emsp;`state` | Rule status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`mark` | fwmark and optionally its mask to match | `optional` | `string` | `0xFF` |
| &emsp;`in` | Incoming logical interface name | `optional` | `string` | `lan` |
| &emsp;`out` | Outgoing logical interface name | `optional` | `string` | `wan` |
| &emsp;`src` | Source subnet to match (CIDR notation) | `optional` | `string` | `172.16.0.0/16` |
| &emsp;`dest` | Destination subnet to match (CIDR notation) | `optional` | `string` | `192.168.1.0/24` |
| &emsp;`invert` | Invert the meaning of the match options | `optional` | `boolean` | `0` |
| &emsp;`priority` | Priority of the IP rule | `optional` | `integer` | `100` |
| &emsp;`lookup` | Protocol ID to use for the route declared in `/etc/iproute2/rt_tables` | `mandatory` | `string` | `main` |
| &emsp;`goto` | Rule to jump to specified by its `priority` value | `optional` | `integer` | `99` |
| &emsp;`action` | Routing action (`prohibit`, `unreachable`, `blackhole` or `throw`) | `optional` | `string` | `prohibit` |
| &emsp;`disabled` | Disable the rule | `optional` | `boolean` | `0` |
| `network_routes` | List of [network routes](https://openwrt.org/docs/guide-user/network/routing/routes_configuration) |  | (`list of dictionaries`) |  |
| &emsp;`id` | Unique route ID | `mandatory` | `string` | `route_example` |
| &emsp;`state` | Route status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`interface` | Logical interface name of the parent (or master) interface the route belongs to | `mandatory` | `string` | `lan` |
| &emsp;`target` | Network address | `mandatory` | `string` | `172.16.0.10` |
| &emsp;`netmask` | Route netmask | `optional` | `string` | `255.255.255.255` |
| &emsp;`gateway` | Network gateway | `optional` | `string` | `172.16.0.1` |
| &emsp;`table` | Table ID to use for the route | `optional` | `string` | `main` |
| &emsp;`source` | Route source address in source-address dependent routes | `optional` | `boolean` | `0` |
| &emsp;`type` | Routing type (`unicast`, `local`, `broadcast`, `multicast`, `unreachable`, `prohibit`, `blackhole` or `anycast`) | `optional` | `string` | `unicast` |
| &emsp;`proto` | Protocol ID to use for the route declared in `/etc/iproute2/rt_tables` | `mandatory` | `string` | `main` |
| &emsp;`disabled` | Disable the rule | `optional` | `boolean` | `0` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.network
```

## Example Vars

```yaml
network_devices:
  - id: "iot_dev"
    name: "br-iot"
    state: "present"
    type: "bridge"
network_interfaces:
  - id: "lan"
    proto: "static"
    ipaddr: "192.168.1.1"
    netmask: "255.255.255.0"
  - id: "wan"
    proto: "dhcp"
  - id: "iot"
    state: "present"
    device: "br-iot"
    proto: "static"
    auto: "1"
    force_link: "1"
    ipaddr: "192.168.2.1"
    netmask: "255.255.255.0"
  - id: "wan6"
    state: "absent"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/base-system/basic-networking
- https://openwrt.org/docs/guide-user/network/ucicheatsheet
- https://openwrt.org/docs/guide-user/network/routing/ip_rules
- https://openwrt.org/docs/guide-user/network/routing/routes_configuration
- https://openwrt.org/docs/guide-user/network/wan/wan_interface_protocols
