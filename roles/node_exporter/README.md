# `flyoverhead.openwrt.node_exporter`

OpenWRT `Prometheus node-exporter` configuration
- install and configure node-exporter service

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `node_exporter_pkgs` | List of node-exporter packages to be installed | `required` | `list` | `  ["prometheus-node-exporter-lua", "prometheus-node-exporter-lua-nat_traffic", "prometheus-node-exporter-lua-netstat", "prometheus-node-exporter-lua-openwrt", "prometheus-node-exporter-lua-wifi", "prometheus-node-exporter-lua-wifi_stations",]` |
| `node_exporter_settings` | Prometheus node-exporter settings configuration |  | `dictionary` |  |
| &emsp;`listen_interface` | Node-exporter listening interface | `mandatory` | `string` | `lan` |
| &emsp;`listen_port` | Node-exporter listening port | `mandatory` | `integer` | `9100` |
| &emsp;`listen_ipv6` | Enable IPv6 support | `optional` | `boolean` | `0` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.node_exporter
```

## Example Vars

```yaml
node_exporter_settings:
  listen_interface: "lan"
  listen_port: "9100"
  listen_ipv6: "0"

```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://www.cloudrocket.at/posts/monitor-openwrt-nodes-with-prometheus
- https://grafana.com/blog/2021/02/09/how-i-monitor-my-openwrt-router-with-grafana-cloud-and-prometheus