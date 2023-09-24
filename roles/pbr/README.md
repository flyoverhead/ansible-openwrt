# `flyoverhead.openwrt.pbr`

OpenWRT `Policy-Based Routing` configuration
- create and configure pbr rules and routes

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `pbr_pkgs` | List of PBR packages to be installed | `required` | `list` | `["pbr", "luci-app-pbr"]` |
| `dnsmasq_full_required_version` | Dnsmasq-full package version required for dnsmasq nft sets support | `required` | `string` | `2.89` |
| `pbr_service` | PBR service configuration settings to be applied |  | `dictionary` |  |
| &emsp;`enabled` | Enable pbr service | `required` | `boolean` | `1` |
| &emsp;`verbosity` | Console and system log output verbosity (`0`, `1` or `2`) | `optional` | `integer` | `2` |
| &emsp;`strict_enforcement` | Enforce policies when their interface is down | `required` | `boolean` | `1` |
| &emsp;`resolver_set` | Resolver’s set support for domains (`none`, `adguardhome.ipset`, `dnsmasq.ipset` or `dnsmasq.nftset`) | `required` | `string` | `dnsmasq.nftset` |
| &emsp;`resolver_instance` | List of resolver’s set support instances (available for `dnsmasq.ipset` and `dnsmasq.nftset`) | `optional` | `string` | `*` |
| &emsp;`ipv6_enabled` | Enable IPv6 support | `optional` | `boolean` | `0` |
| &emsp;`supported_interface` | List of network interfaces to be explicitly supported by the pbr service | `optional` | `list` | `["lan", "wan"]` |
| &emsp;`ignored_interface` | List of network interfaces to be ignored by the pbr service | `optional` | `list` | `["vpnserver", "wgserver"]` |
| &emsp;`boot_timeout` | Time in seconds for pbr service to wait for WAN gateway discovery on boot | `optional` | `integer` | `30` |
| &emsp;`rule_create_option` | Policy rule creation option (`add` or `insert`) | `required` | `string` | `add` |
| &emsp;`icmp_interface` | Default ICMP protocol network interface | `optional` | `string` | `wan` |
| &emsp;`wan_mark` | Firewall mark for marks used by the pbr service | `optional` | `string` | `010000` |
| &emsp;`fw_mask` | Firewall mask used by the pbr service | `optional` | `string` | `ff0000` |
| &emsp;`secure_reload` | Enable killing router traffic (activates killswitch) during service start/restart/reload operations to prevent traffic leaks on unwanted interface (`experimental`) | `optional` | `boolean` | `0` |
| &emsp;`webui_show_ignore_target` | Show `ignore` in the list of interfaces | `optional` | `boolean` | `0` |
| &emsp;`webui_supported_protocol` | List of protocols to display in the `Protocol` column for policies (`all`, `tcp`, `udp` or `icmp`) | `optional` | `list` | `0` |
| `pbr_policies` | List of PBR policies |  | `list of dictionaries` |  |
| &emsp;`id` | Unique policy ID | `mandatory` | `string` | `example_policy` |
| &emsp;`name` | Unique policy name | `mandatory` | `string` | `Example policy` |
| &emsp;`state` | Policy status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`enabled` | Enable policy | `required` | `boolean` | `1` |
| &emsp;`interface` | Policy associated network interface | `mandatory` | `string` | `vpn0` |
| &emsp;`src_addr` | List of local/source IP addresses, CIDRs, hostnames, mac addresses, local physical devices or URLs to list of addresses (not compatible with the `secure_reload` option) | `required` | `list` | `["192.168.1.0/24"]` |
| &emsp;`src_port` | List of space-separated local/source ports or port-ranges | `required` | `list` | `["22", "8000-9000"]` |
| &emsp;`dest_addr` | List of remote/target IP addresses, CIDRs, hostnames/domain names or URLs to list of addresses | `required` | `list` | `["192.168.2.0/24"]` |
| &emsp;`dest_port` | List of space-separated remote/target ports or port-ranges | `required` | `list` | `["22", "8000-9000"]` |
| &emsp;`proto` | Policy protocol (any valid protocol from `/etc/protocols` for CLI/uci or selected from the values set in `webui_supported_protocol`) | `required` | `string` | `auto` |
| &emsp;`chain` | Policy chain (`forward`, `input`, `prerouting`, `postrouting` or `output`) | `required` | `string` | `prerouting` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.pbr
```

## Example Vars

```yaml
pbr_service:
  enabled: "1"
  verbosity: "0"
  strict_enforcement: "0"
  resolver_set: "dnsmasq.nftset"
  ipv6_enabled: "0"
  ignored_interface: ["vpnserver"]
  boot_timeout: "30"
  rule_create_option: "add"
  webui_show_ignore_target: "0"
  webui_supported_protocol: ["all", "tcp", "udp", "tcp udp", "icmp"]
pbr_policies:
  - id: "example_policy"
    name: "Example policy"
    state: "present"
    enabled: "1"
    interface: "vpn0"
    dest_addr: ["blocked.example.com", "blocked.com"]
    chain: "prerouting"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/network/routing/pbr_app
- https://docs.openwrt.melmac.net/pbr/#a-word-about-default-routing
