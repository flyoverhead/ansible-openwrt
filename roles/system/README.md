# `flyoverhead.openwrt.system`

OpenWRT `system` configuration
- configure system settings

## Role Variables

| Variable | Description | Status | Type | Example |
| :--- | :--- | :--- | :--- | :--- |
| `system_settings` | System settings configuration |  | `dictionary` |  |
| &emsp;`hostname` | Device hostname | `required` | `string` | `OpenWrt` |
| &emsp;`description` | Short device description | `optional` | `string` | `OpenWrt device` |
| &emsp;`klogconloglevel` | Maximum log level for kernel messages | `optional` | `integer` | `7` |
| &emsp;`log_file` | Path to file to write log messages to | `optional` | `string` | `/var/log/messages` |
| &emsp;`log_hostname` | Hostname sent with remote syslog messages | `optional` | `string` | `OpenWrt` |
| &emsp;`log_ip` | IP address of a remote syslog server to which the log messages should be sent | `optional` | `string` | `192.168.1.1` |
| &emsp;`log_port` | Port number of a remote syslog server | `optional` | `integer` | `514` |
| &emsp;`log_proto` | Protocol used for the connection with a remote syslog server | `optional` | `string` | `udp` |
| &emsp;`log_remote` | Enable remote logging | `optional` | `boolean` | `1` |
| &emsp;`log_size` | Size of the file based log buffer in KiB | `optional` | `integer` | `64` |
| &emsp;`log_type` | Log type (`circular` or `file`) | `optional` | `string` | `circular` |
| &emsp;`timezone` | POSIX.1 format system timezone | `required` | `string` | `UTC` |
| &emsp;`zonename` | IANA/Olson format system timezone | `required` | `string` | `UTC` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.system
```

## Example Vars

```yaml
system_settings:
  hostname: "mikrotik"
  description: "MikroTik hAP ac2"
  timezone: "UTC"
  zonename: "UTC"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/base-system/system_configuration
