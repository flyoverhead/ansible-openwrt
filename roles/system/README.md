# `flyoverhead.openwrt.system`

OpenWRT `system` configuration
- configure system settings

## Role Variables

| Variable | Descritpion | Status | Type | Example |
| :--- | :--- | :--- | :--- | :--- |
| `system` | System settings configuration |  | `dictionary` |  |
| &emsp;`system_hostname` | Device hostname | `required` | `string` | `OpenWrt` |
| &emsp;`system_description` | Short device description | `optional` | `string` | `OpenWrt device` |
| &emsp;`system_klogconloglevel` | Maximum log level for kernel messages | `optional` | `integer` | `7` |
| &emsp;`system_log_file` | Path to file to write log messages to | `optional` | `string` | `/var/log/messages` |
| &emsp;`system_log_ip` | IP address of a remote syslog server to which the log messages should be sent | `optional` | `string` | `192.168.1.1` |
| &emsp;`system_log_port` | Port number of a remote syslog server | `optional` | `integer` | `514` |
| &emsp;`system_log_proto` | Protocol used for the connection with a remote syslog server | `optional` | `string` | `udp` |
| &emsp;`system_log_remote` | Enable remote logging | `optional` | `boolean` | `1` |
| &emsp;`system_log_size` | Size of the file based log buffer in KiB | `optional` | `integer` | `64` |
| &emsp;`system_log_type` | Log type (`circular` or `file`) | `optional` | `string` | `circular` |
| &emsp;`system_timezone` | POSIX.1 format system timezone | `required` | `string` | `UTC` |
| &emsp;`system_zonename` | IANA/Olson format system timezone | `required` | `string` | `UTC` |

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
system:
  hostname: "mikrotik"
  description: "MikroTik hAP ac2"
  timezone: UTC"
  zonename: "UTC"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/base-system/system_configuration
