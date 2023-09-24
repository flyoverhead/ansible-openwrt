# `flyoverhead.openwrt.wireless`

OpenWRT `wireless` configuration
- configure wireless devices
- create/delete/configure wireless interfaces

## Role Variables

| Variable | Descritpion | Status | Type | Example |
| :--- | :--- | :--- | :--- | :--- |
| `wireless_devices` | List of [wireless devices](https://openwrt.org/docs/guide-user/network/wifi/basic#wi-fi_devices) |  | `list of dictionaries` |  |
| &emsp;`id` | Radio device ID | `mandatory` | `string` | `radio0` |
| &emsp;`type` | Radio device type | `optional` | `string` | `mac80211` |
| &emsp;`disabled` | Disable radio adapter | `required` | `boolean` | `0` |
| &emsp;`channel` | Wireless channel (`integer` of `auto`) | `optional` | `string` | `auto` |
| &emsp;`band` | Wireless band (`2g` or `5g`) | `optional` | `string` | `2g` |
| &emsp;`htmode` | Wireless channel width | `optional` | `string` | `HT20` |
| &emsp;`country` | Country code for adaptive channels and transmission powers | `optional` | `string` | `US` |
| `wireless_interfaces` | List of [wireless interfaces](https://openwrt.org/docs/guide-user/network/wifi/basic#wi-fi_interfaces) |  | `list of dictionaries` |  |
| &emsp;`id` | Unique interface ID | `mandatory` | `string` | `iot0` |
| &emsp;`name` | Unique interface name | `mandatory` | `string` | `iot0` |
| &emsp;`state` | Interface status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`device` | Radio device associated with the interface | `mandatory` | `string` | `radio0` |
| &emsp;`network` | List of networks associated with the interface | `required` | `list` | `iot` |
| &emsp;`mode` | Wireless interface operation mode | `required` | `string` | `ap` |
| &emsp;`disabled` | Disable wireless network | `required` | `boolean` | `0` |
| &emsp;`ssid` | Wireless network SSID | `required` | `string` | `OpenWrt` |
| &emsp;`mesh_id` | ID of the mesh network (as defined in IEEE 802.11s) to join wireless interface to | `optional` | `string` | `example_mesh_id` |
| &emsp;`hidden` | Disable broadcasting of beacon frames to hide ESSID | `optional` | `boolean` | `0` |
| &emsp;`isolate` | Isolate wireless clients from each other | `optional` | `boolean` | `0` |
| &emsp;`encryption` | Wireless encryption method | `required` | `string` | `psk2` |
| &emsp;`key` | Passphrase for pre-shared key | `required` | `string` | `password` |
| &emsp;`macfilter` | MAC filter policy (`disable`, `allow` or `deny`) | `optional` | `string` | `disable` |
| &emsp;`maclist` | List of MAC addresses used fot MAC filter policy | `optional` | `list` | `["00:11:22:33:44:55", "55:44:33:22:11:00"]` |
| &emsp;`ieee80211r` | Enable fast BSS transition (802.11r) support | `optional` | `boolean` | `0` |
| &emsp;`mobility_domain` | Enable Mobility Domain identifier used to indicate a group of APs between which a STA can use fast BSS transition (will be generated automatically) | `optional` | `boolean` | `1` |
| &emsp;`ft_over_ds` | Enable FT-over-DS | `optional` | `boolean` | `1` |
| &emsp;`ft_psk_generate_local` | Enable local generation of FT response for PSK networks | `optional` | `boolean` | `1` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.wireless
```

## Example Vars

```yaml
wireless_devices:
  - id: "radio0"
    type: "mac80211"
    channel: "auto"
wireless_interfaces:
  - id: "lan0"
    name: "lan0"
    state: "present"
    device: "radio0"
    network: ["lan"]
    mode: "ap"
    ssid: "openwrt"
    encryption: "psk2"
    key: "password"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/network/wifi/basic
