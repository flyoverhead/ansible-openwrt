# `flyoverhead.openwrt.firewall`

OpenWRT `firewall` configuration
- configure defaults settings
- create/delete/configure firewall zones
- create/delete/configure firewall zone forwardings
- create/delete/configure firewall traffic rules
- create/delete/configure firewall port forwardings
- create/delete/configure firewall ip sets
- create/delete/configure firewall nat rules

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `firewall_defaults` | [Minimal firewall configuration](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#defaults) |  | `dictionary` |  |
| &emsp;`input` | Set policy for the `INPUT` chain of the `filter` table (`ACCEPT`, `REJECT`, `DROP`) | `optional` | `string` | `REJECT` |
| &emsp;`forward` | Set policy for the `FORWARD` chain of the `filter` table (`ACCEPT`, `REJECT`, `DROP`) | `optional` | `string` | `REJECT` |
| &emsp;`output` | Set policy for the `OUTPUT` chain of the `filter` table (`ACCEPT`, `REJECT`, `DROP`) | `optional` | `string` | `REJECT` |
| &emsp;`drop_invalid` | Drop invalid packets | `optional` | `boolean` | `0` |
| &emsp;`synflood_protect` | Enable SYN flood protection | `optional` | `boolean` | `0` |
| &emsp;`synflood_rate` | Set rate limit (packets/second) for SYN packets which the traffic is considered a flood | `optional` | `string` | `25/s` |
| &emsp;`synflood_burst` | Set burst limit for SYN packets which the traffic is considered a flood if it exceeds the allowed rate | `optional` | `string` | `50` |
| &emsp;`flow_offloading` | Enable software flow offloading for connections | `optional` | `boolean` | `0` |
| &emsp;`flow_offloading_hw` | Enable hardware flow offloading for connections | `optional` | `boolean` | `0` |
| `firewall_zones` | List of [firewall zones](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#zones) |  | `list of dictionaries` |  |
| &emsp;`id` | Unique zone ID | `mandatory` | `string` | `guest` |
| &emsp;`name` | Unique zone name | `mandatory` | `string` | `guest` |
| &emsp;`state` | Zone status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`network` | List of interfaces attached to this zone (if omitted and neither extra* options, subnets nor devices are given, the value of `name` is used by default) | `required` | `list` | `["lan"]` |
| &emsp;`masq` | Masquerade outgoing zone IPv4 traffic  | `optional` | `boolean` | `0` |
| &emsp;`mtu_fix` | Enable MSS clamping for outgoing zone traffic  | `optional` | `boolean` | `0` |
| &emsp;`input` | Default policy for incoming zone traffic (`ACCEPT`, `REJECT`, `DROP`) | `optional` | `string` | `DROP` |
| &emsp;`forward` | Default policy for forwarded zone traffic (`ACCEPT`, `REJECT`, `DROP`) | `optional` | `string` | `DROP` |
| &emsp;`output` | Default policy for outgoing zone traffic (`ACCEPT`, `REJECT`, `DROP`) | `optional` | `string` | `DROP` |
| &emsp;`family` | Protocol family used by iptables rule (`ipv4`, `ipv6` or `any`) | `optional` | `string` | `any` |
| &emsp;`enabled` | Enable zone | `optional` | `boolean` | `1` |
| `firewall_forwardings` | List of [zone forwardings](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#forwardings) |  | (`list of dictionaries`) |  |
| &emsp;`name` | Unique forwarding name | `mandatory` | `string` | `guest` |
| &emsp;`state` | Forwarding status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`src` | Traffic source zone (one of the defined `zone names`) | `mandatory` | `string` | `lan` |
| &emsp;`dest` | Traffic destination zone (one of the defined `zone names`) | `mandatory` | `string` | `wan` |
| &emsp;`family` | Protocol family used by iptables rule (`ipv4`, `ipv6` or `any`) | `optional` | `string` | `any` |
| &emsp;`ipset` | Match traffic against the given ipset | `optional` | `string` | `ipset_example` |
| &emsp;`enabled` | Enable forwarding | `optional` | `boolean` | `1` |
| `firewall_rules` | List of [traffic rules](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#rules) |  | (`list of dictionaries`) |  |
| &emsp;`id` | Unique rule ID | `mandatory` | `string` | `rule_example` |
| &emsp;`name` | Unique rule name | `mandatory` | `string` | `Example rule` |
| &emsp;`state` | Rule status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`src` | Traffic source zone (one of the defined `zone names` or `*` for any zone) | `required` | `string` | `wan` |
| &emsp;`src_ip` | Match incoming traffic from the specified source IP address | `optional` | `string` | `192.168.1.1` |
| &emsp;`src_port` | Match incoming traffic from the specified source port or port range, if relevant `proto` is specified | `optional` | `list` | `["22", "80", "3000-3010"]` |
| &emsp;`src_mac` | Match incoming traffic from the specified MAC address | `optional` | `string` | `00:11:22:33:44:55` |
| &emsp;`dest` | Traffic destination zone (one of the defined `zone names` or `*` for any zone) | `required` | `string` | `lan` |
| &emsp;`dest_ip` | Match incoming traffic directed to the specified destination IP address | `optional` | `string` | `192.168.1.10` |
| &emsp;`dest_port` | Match incoming traffic directed at the given destination port or port range, if relevant `proto` is specified | `optional` | `list` | `["22", "80", "3000-3010"]` |
| &emsp;`target` | Firewall action (`ACCEPT`, `REJECT`, `DROP`, `MARK`, `NOTRACK`) for matched traffic | `mandatory` | `string` | `DROP` |
| &emsp;`proto` | Match incoming traffic using the given protocol (`tcp`, `udp`, `udplite`, `icmp`, `esp`, `ah`, `sctp`, or `all`) | `optional` | `list` | `["tcp", "udp"]` |
| &emsp;`family` | Protocol family used by iptables rule (`ipv4`, `ipv6` or `any`) | `optional` | `string` | `any` |
| &emsp;`ipset` | Match traffic against the given ipset | `optional` | `string` | `ipset_example` |
| &emsp;`mark` | Match traffic against the given firewall mark | `optional` | `string` | `prohibit` |
| &emsp;`set_mark` | Zeroes out the bits given by mask and ORs value into the packet mark | `optional` | `string` | `0xFFFFFFFF` |
| &emsp;`enabled` | Enable rule | `optional` | `boolean` | `0` |
| `firewall_redirects` | List of [port forwardings](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#redirects) |  | (`list of dictionaries`) |  |
| &emsp;`id` | Unique redirect ID | `mandatory` | `string` | `redirect_example` |
| &emsp;`name` | Redirect description | `mandatory` | `string` | `Example redirection` |
| &emsp;`state` | Redirect status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`src` | Traffic source zone (one of the defined `zone names`) | `required` | `string` | `wan` |
| &emsp;`src_ip` | Match incoming traffic from the specified source IP address | `optional` | `string` | `172.16.0.10` |
| &emsp;`src_port` | Match incoming traffic originating from the given source port or port range on the client host | `optional` | `string` | `22` |
| &emsp;`src_mac` | Match incoming traffic from the specified MAC address | `optional` | `string` | `00:11:22:33:44:55` |
| &emsp;`src_dip` | Match incoming traffic directed at the given destination IP address (for DNAT), or rewrite the source address to the given address (for SNAT) | `optional` | `string` | `172.16.0.10` |
| &emsp;`src_dport` | Match incoming traffic directed at the given destination port or port range (for DNAT), or rewrite the source ports to the given value (for SNAT) | `optional` | `string` | `22` |
| &emsp;`dest` | Traffic destination zone (one of the defined `zone names`) | `required` | `string` | `lan` |
| &emsp;`dest_ip` | Redirect matches incoming traffic to the specified internal host (for DNAT), or match traffic directed at the given address (for SNAT) | `optional` | `string` | `192.168.1.1` |
| &emsp;`dest_port` | Redirect matched incoming traffic to the given port or port range (for DNAT), or match traffic directed at the given port or port range (for SNAT) | `optional` | `string` | `22` |
| &emsp;`target` | NAT target for the rule (`DNAT` or `SNAT`) | `optional` | `string` | `DNAT` |
| &emsp;`proto` | Match incoming traffic using the given protocol (`tcp`, `udp`, `udplite`, `icmp`, `esp`, `ah`, `sctp`, or `all`) | `optional` | `list` | `["tcp", "udp"]` |
| &emsp;`family` | Protocol family used by iptables rule (`ipv4`, `ipv6` or `any`) | `optional` | `string` | `any` |
| &emsp;`ipset` | Match traffic against the given ipset | `optional` | `string` | `ipset_example` |
| &emsp;`mark` | Match traffic against the given firewall mark | `optional` | `string` | `0xFFFFFFFF` |
| &emsp;`enabled` | Enable redirect | `optional` | `boolean` | `1` |
| `firewall_ipsets` | List of [IP sets](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#options_fw4) |  | (`list of dictionaries`) |  |
| &emsp;`id` | Unique ipset ID | `mandatory` | `string` | `ipset_example` |
| &emsp;`name` | Unique ipset name | `mandatory` | `string` | `Example IPset` |
| &emsp;`state` | Forwarding status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`comment` | Comment for the ipset | `optional` | `string` | `Example IP set` |
| &emsp;`match` | List of matched data types (`ip`, `port`, `mac`, `net` or `set`) and their direction (`src` or `dest`) joined by an underscore | `mandatory` | `list` | `["src_ip", "port"]` |
| &emsp;`entry` | The IP address, CIDR, or MAC | `optional` | `list` | `["00:11:22:33:44:55", "172.16.0.0/24"]` |
| &emsp;`family` | Protocol family used by ipset (`ipv4`, `ipv6` or `any`) | `optional` | `string` | `ipv4` |
| &emsp;`maxelem` | Limits of items that can be added to the ipset | `optional` | `integer` | `65536` |
| &emsp;`loadfile` | Path to file containing a list of CIDRs, subnets, host IPs | `optional` | `string` | `/tmp/ipset.list` |
| &emsp;`enabled` | Enable ipset | `optional` | `boolean` | `1` |
| `firewall_nats` | List of [NAT rules](https://openwrt.org/docs/guide-user/firewall/fw3_configurations/fw3_nat#selective_nat) |  | (`list of dictionaries`) |  |
| &emsp;`id` | Unique nat ID | `mandatory` | `string` | `nat_example` |
| &emsp;`name` | Unique rule name | `mandatory` | `string` | `Example NAT` |
| &emsp;`state` | Redirect status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`src` | Traffic source zone (one of the defined `zone names`) | `required` | `string` | `wan` |
| &emsp;`src_ip` | Match forwarded traffic from the specified source IP address or range | `optional` | `string` | `172.16.0.10` |
| &emsp;`src_port` | Match forwarded traffic originating from the given source port or port range | `optional` | `string` | `22` |
| &emsp;`dest_ip` | Match forwarded traffic directed at the given IP address | `optional` | `string` | `192.168.1.1` |
| &emsp;`dest_port` | Match forwarded traffic directed at the given destination port or port range | `optional` | `string` | `22` |
| &emsp;`snat_ip` | Rewrite matched traffic to the specified source IP address (for `SNAT`) | `optional` | `string` | `172.16.0.10` |
| &emsp;`snat_port` | Rewrite matched traffic to the specified source port or port range | `optional` | `string` | `22` |
| &emsp;`target` | NAT target for the rule (`ACCEPT`, `MASQUERADE` or `SNAT`) | `mandatory` | `string` | `ACCEPT` |
| &emsp;`proto` | Match incoming traffic using the given protocol (`tcp`, `udp`, `udplite`, `icmp`, `esp`, `ah`, `sctp`, or `all`) | `optional` | `list` | `["tcp", "udp"]` |
| &emsp;`family` | Protocol family used by ipset (`ipv4`, `ipv6` or `any`) | `optional` | `string` | `ipv4` |
| &emsp;`mark` | Match traffic against the given firewall mark | `optional` | `string` | `0xFFFFFFFF` |
| &emsp;`enabled` | Enable NAT rule | `optional` | `boolean` | `0` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.firewall
```

## Example Vars

```yaml
firewall_defaults:
  input: "ACCEPT"
  forward: "REJECT"
  output: "ACCEPT"
  drop_invalid: "1"
  synflood_protect: "1"
firewall_zones:
  - id: "iot"
    name: "iot"
    state: "present"
    network: ["iot"]
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
    name: "Allow DHCP and DNS for IoT"
    state: "present"
    src: "iot"
    src_port: ["53", "67", "68"]
    dest: "lan"
    dest_port: ["53", "67", "68"]
    target: "ACCEPT"
    family: "ipv4"
firewall_redirects:
  - id: "force_dns_lan"
    name: "Force lan DNS"
    state: "present"
    src: "lan"
    src_dport: "53"
    target: "DNAT"
    family: "ipv4"
  - id: "force_dns_iot"
    name: "Force iot DNS"
    state: "present"
    src: "iot"
    src_dport: "53"
    target: "DNAT"
    family: "ipv4"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/firewall/firewall_configuration
- https://openwrt.org/docs/guide-user/firewall/fw3_configurations/fw3_nat#nat_examples
