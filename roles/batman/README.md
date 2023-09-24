# `flyoverhead.openwrt.batman`

OpenWRT `B.A.T.M.A.N.` configuration
- create and configure B.A.T.M.A.N. network

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `batman_enabled` | Enable B.A.T.M.A.N. configuration | `required` | `boolean` | `false` |
| `ath10k_ct_fix` | Enable [ath10k-ct drivers fix](https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s#wireless_hardware_support) | `optional` | `boolean` | `false` |
| `non_mesh_pkgs` | List of non-mesh wpad packages installed by default | `required` | `list` | `["wpad-mini", "wpad-basic", "wpad-basic-wolfssl", "wpad-basic-openssl", "wpad-basic-mbedtls"]` |
| `batman_pkgs` | List of B.A.T.M.A.N. packages to be installed | `required` | `list` | `["wpad-mesh-wolfssl", "kmod-batman-adv", "batctl-default"]` |
| `batman_network_interfaces` | List of [B.A.T.M.A.N. network interfaces](https://openwrt.org/docs/guide-user/network/wifi/mesh/batman#batman-adv_options_for_bat0_the_main_mesh_interface) to be configured |  | `list of dictionaries` |  |
|  | B.A.T.M.A.N. control interface configuration |  |  |  |
| &emsp;`id` | Unique B.A.T.M.A.N. control interface ID | `mandatory` | `string` | `bat0` |
| &emsp;`state` | Interface status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`proto` | Interface protocol | `mandatory` | `string` | `batadv` |
| &emsp;`aggregated_ogms` | Enable originator messages used to determine the qualities needed to direct neighbors and spreading this message throughout the whole mesh | `required` | `boolean` | `1` |
| &emsp;`routing_algo` | Routing algorithm (`BATMAN_IV` or `BATMAN_V`) | `mandatory` | `string` | `BATMAN_IV` |
| &emsp;`bonding` | Distribute frames through interfaces (similar in quality and speed) using Round Robin to increase throughput | `optional` | `boolean` | `0` |
| &emsp;`fragmentation` | Enable packet fragmentation | `required` | `boolean` | `1` |
| &emsp;`gw_mode` | Gateway mode (`off`, `client` or `server`) | `required` | `string` | `off` |
| &emsp;`gw_bandwidth` | Gateway bandwidth (for `client` nodes to know about the gateway's quality stated by `download/upload`, and can be suffixed with `mbit` or `kbit`) | `optional` | `string` | `100mbit/20mbit` |
| &emsp;`gw_sel_class` | Criteria by which client shoul select a gateway indicated by TQ (`1`, `2`, `3` or `XX`) | `optional` | `integer` | `20` |
| &emsp;`log_level` | Standard warning/error messages are sent to the kernel log (`0-255` 8 bit Bitmask) | `optional` | `integer` | `0` |
| &emsp;`orig_interval` | Interval (in milliseconds) in which batman-adv floods the network with its protocol information | `optional` | `integer` | `1000` |
| &emsp;`bridge_loop_avoidance` | Enable avoidance of broadcast loops (for bridged LAN setups) | `required` | `boolean` | `1` |
| &emsp;`distributed_arp_table` | Enable mesh-wide ARP table cache | `required` | `boolean` | `1` |
| &emsp;`multicast_mode` | Enable group aware multicast forwarding infrastructure, aiming to reduce unnecessary packet transmissions | `required` | `boolean` | `1` |
| &emsp;`multicast_fanout` | Batman-adv detects potential multicast listeners who are interested in traffic to a given multicast destination address (requires `multicast_mode`) | `optional` | `integer` | `16` |
| &emsp;`network_coding` | Enable to combine two packets into a single transmission | `optional` | `boolean` | `0` |
| &emsp;`hop_penalty` | Modify batman_adv's preference for multihop routes vs short routes | `optional` | `integer` | `30` |
| &emsp;`ap_isolation` | Enable standard WiFi APs support AP Isolation to prevent clients communicating with each other | `optional` | `boolean` | `0` |
| &emsp;`isolation_mark` | Firewall mark which allows to classify clients as isolated via firewall rules (defined as a `value/mask`) | `optional` | `string` | `0x00000000/0x00000000` |
|  | B.A.T.M.A.N. link interface configuration |  |  |  |
| &emsp;`name` | Unique B.A.T.M.A.N. link interface name | `mandatory` | `string` | `batmesh` |
| &emsp;`state` | Interface status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`proto` | Interface protocol | `mandatory` | `string` | `batadv_hardif` |
| &emsp;`master` | B.A.T.M.A.N. control interface name | `mandatory` | `string` | `bat0` |
| &emsp;`mtu` | MTU value | `optional` | `integer` | `2304` |
| &emsp;`delegate` | Enable prefix delegation | `optional` | `boolean` | `0` |
| `batman_wireless_interfaces` | List of [B.A.T.M.A.N. network interfaces](https://openwrt.org/docs/guide-user/network/wifi/mesh/batman#s_encrypted_authenticated_mesh) |  | `list of dictionaries` |  |
| &emsp;`id` | Unique wireless interface ID | `mandatory` | `string` | `mesh0` |
| &emsp;`name` | Unique wireless interface name | `mandatory` | `string` | `mesh0` |
| &emsp;`state` | Interface status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`device` | Radio device associated with the interface | `mandatory` | `string` | `radio0` |
| &emsp;`network` | B.A.T.M.A.N. network associated with the interface (match `B.A.T.M.A.N. link interface name`) | `required` | `list` | `batmesh` |
| &emsp;`mode` | Wireless interface operation mode | `required` | `string` | `mesh` |
| &emsp;`disabled` | Disable wireless network | `required` | `boolean` | `0` |
| &emsp;`mesh_id` | ID of the mesh network to join wireless interface to | `optional` | `string` | `example_mesh_id` |
| &emsp;`mesh_fwding` | Switch off 802.11s forwarding/routing cause it's handled by batman-adv at each node | `required` | `boolean` | `0` |
| &emsp;`encryption` | Wireless encryption method | `required` | `string` | `psk2+ccmp` |
| &emsp;`key` | Passphrase for pre-shared key | `required` | `string` | `password` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.batman
```

## Example Vars

### Server Node

```yaml
batman_enabled: true
ath10k_ct_fix: true
batman_network_interfaces:
  - id: "bat0"
    state: "present"
    proto: "batadv"
    routing_algo: "BATMAN_IV"
    gw_mode: "server"
    gw_bandwidth: "100mbit/20mbit"
  - id: "batmesh0"
    state: "present"
    proto: "batadv_hardif"
    master: "bat0"
    mtu: "2304"
batman_wireless_interfaces:
  - id: "mesh0"
    name: "mesh0"
    state: "present"
    device: "radio0"
    network: ["batmesh0"]
    mode: "mesh"
    mesh_id: "openwrt_mesh"
    mesh_fwding: "0"
    encryption: "psk2+ccmp"
    key: "passphrase"
```

### Client Node

```yaml
batman_enabled: true
ath10k_ct_fix: true
batman_network_interfaces:
  - id: "bat0"
    state: "present"
    proto: "batadv"
    routing_algo: "BATMAN_IV"
    gw_mode: "client"
    gw_sel_class: "20"
  - id: "batmesh0"
    state: "present"
    proto: "batadv_hardif"
    master: "bat0"
    mtu: "2304"
batman_wireless_interfaces:
  - id: "mesh0"
    name: "mesh0"
    state: "present"
    device: "radio0"
    network: ["batmesh0"]
    mode: "mesh"
    mesh_id: "openwrt_mesh"
    mesh_fwding: "0"
    encryption: "psk2+ccmp"
    key: "passphrase"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/network/wifi/mesh/batman
- https://downloads.open-mesh.org/batman/manpages/batctl.8.html
