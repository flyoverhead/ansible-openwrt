# `flyoverhead.openwrt.wireguard`

OpenWRT `wireguard` configuration
- create and configure wireguard interfaces
- create and configure wireguard peers

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `wireguard_pkgs` | List of wireguard packages to be installed | `required` | `list` | `["wireguard-tools", "luci-proto-wireguard", "luci-app-wireguard", "qrencode"]` |
| `wireguard_interfaces` | List of [wireguard network interfaces](https://openwrt.org/docs/guide-user/services/vpn/wireguard/server#network) to be configured |  | `list of dictionaries` |  |
| &emsp;`id` | Unique wireguard server interface ID | `mandatory` | `string` | `wg0` |
| &emsp;`state` | Interface status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`proto` | Interface protocol | `mandatory` | `string` | `wireguard` |
| &emsp;`private_key` | Wireguard server private key (will be generated automatically when omit) | `optional` | `string` | `null` |
| &emsp;`addresses` | Wireguard server IP address in CIDR notation | `required` | `string` | `192.168.10.1/32` |
| &emsp;`listen_port` | Wireguard server listening port | `required` | `string` | `51820` |
| &emsp;`peers` | List of wireguard peers to be generated automatically | `required` | `list` | `["mobile", "desktop"]` |
| `wireguard_peers` | List of [wireguard peers](https://openwrt.org/docs/guide-user/services/vpn/wireguard/client#network) |  | `list of dictionaries` |  |
| &emsp;`id` | Unique peer ID | `mandatory` | `string` | `example_peer` |
| &emsp;`name` | Unique peer name | `mandatory` | `string` | `Wireguard peer` |
| &emsp;`state` | Peer status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`public_key` | Peer public key | `mandatory` | `string` | `example_public_key` |
| &emsp;`preshared_key` | Peer preshared key | `required` | `list` | `example_preshared_key` |
| &emsp;`endpoint_host` | Wireguard server (endpoint) public IP address | `required` | `string` | `192.168.2.1` |
| &emsp;`endpoint_port` | Wireguard server (endpoint) listening port | `required` | `integer` | `51820` |
| &emsp;`route_allowed_ips` | Create routes for allowed IPs | `optional` | `boolean` | `0` |
| &emsp;`persistent_keepalive` | Set keep alive messages interval in seconds | `optional` | `integer` | `25` |
| &emsp;`allowed_ips` | IP addresses and prefixes that are allowed to use inside the tunnel | `required` | `list` | `["0.0.0.0/0"]` |
| &emsp;`wireguard_interface_name` | Wireguard associated interface name | `required` | `string` | `wg0` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.wireguard
```

## Example Vars

### Server Mode

```yaml
wireguard_interfaces:
  - name: "wg0"
    state: "present"
    proto: "wireguard"
    addresses: "192.168.2.1/32"
    port: "51820"
    peers: ["mobile_peer", "desktop_peer"]
```

### Client Mode

```yaml
wireguard_interfaces:
  - id: "wg1"
    state: "present"
    proto: "wireguard"
    private_key: "private_key"
    addresses: "192.168.2.2/32"
wireguard_peers:
  - id: "vps"
    name: "Example VPS"
    state: "present"
    public_key: "public_key"
    preshared_key: "preshared_key"
    endpoint_host: "192.168.2.1"
    endpoint_port: "51820"
    route_allowed_ips: "0"
    persistent_keepalive: 25""
    allowed_ips: ["0.0.0.0/0"]
    wireguard_interface_name: "wg1"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/services/vpn/wireguard/server
- https://openwrt.org/docs/guide-user/services/vpn/wireguard/client
