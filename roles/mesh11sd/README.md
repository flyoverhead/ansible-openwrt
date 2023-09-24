# `flyoverhead.openwrt.mesh`

OpenWRT `mesh` configuration
- create and configure mesh network

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `mesh_enabled` | Enable mesh configuration | `required` | `boolean` | `false` |
| `ath10k_ct_fix` | Enable [ath10k-ct drivers fix](https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s#wireless_hardware_support) | `optional` | `boolean` | `false` |
| `non_mesh_pkgs` | List of non-mesh wpad packages installed by default | `required` | `list` | `["wpad-mini", "wpad-basic", "wpad-basic-wolfssl", "wpad-basic-openssl", "wpad-basic-mbedtls"]` |
| `mesh_pkgs` | List of mesh wpad packages to be installed | `required` | `list` | `["wpad-mesh-wolfssl", "mesh11sd"]` |
| `mesh11sd_interfaces` | List of [wireless interfaces](https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s#the_wireless_uci_config_file) to be configured as mesh nodes |  | `list of dictionaries` |  |
| &emsp;`id` | Unique interface ID | `mandatory` | `string` | `mesh` |
| &emsp;`name` | Unique interface name | `mandatory` | `string` | `mesh` |
| &emsp;`state` | Interface status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`device` | Radio device associated with the interface | `mandatory` | `string` | `radio0` |
| &emsp;`network` | List of networks associated with the interface | `mandatory` | `list` | `["lan"]` |
| &emsp;`mode` | Wireless interface operation mode | `required` | `string` | `mesh` |
| &emsp;`disabled` | Disable wireless network | `optional` | `boolean` | `0` |
| &emsp;`mesh_id` | ID of the mesh network (as defined in IEEE 802.11s) to join wireless interface to | `required` | `string` | `example_mesh_id` |
| &emsp;`encryption` | Wireless encryption method | `required` | `string` | `sae` |
| &emsp;`key` | Passphrase for pre-shared key | `required` | `string` | `password` |
| `mesh_settings` | List of [mesh parameters and options](https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s#mesh11sd_-_setting_parameters_and_options) |  | `list of dictionaries` |  |
| &emsp;`mesh_fwding` | Forward packets to peer mesh nodes | `required` | `boolean` | `1` |
| &emsp;`mesh_rssi_threshold` | Minimum received signal strength from a peer meshnode for a connection to be established | `optional` | `integer` | `-80` |
| &emsp;`mesh_gate_announcements` | Enable gate announcements | `optional` | `boolean` | `1` |
| &emsp;`mesh_hwmp_rootmode` | Mesh convergence level (`0`, `1`, `2`, `3` or `4`) | `required` | `integer` | `2` |
| &emsp;`mesh_max_peer_links` | Maximum number of peer nodes that can connect | `optional` | `integer` | `150` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.mesh
```

## Example Vars

```yaml
mesh_enabled: true
ath10k_ct_fix: true
mesh11sd_interfaces:
  - id: "mesh0"
    name: "mesh0"
    state: "present"
    device: "radio0"
    network: ["lan"]
    mode: "mesh"
    mesh_id: "openwrt_mesh"
    encryption: "sae"
    key: "passphrase"
mesh11sd_settings:
  mesh_fwding: "1"
  mesh_rssi_threshold: "-80"
  mesh_gate_announcements: "1"
  mesh_hwmp_rootmode: "3"
  mesh_max_peer_links: "150"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s
- https://openwrt.org/docs/guide-user/network/wifi/mesh/mesh11sd
