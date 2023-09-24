# `flyoverhead.openwrt.dropbear`

OpenWRT `dropbear` configuration
- configure dropbear settings

## Role Variables

| Variable | Descritpion | Status | Type | Example |
| :--- | :--- | :--- | :--- | :--- |
| `dropbear` | Dropbear settings configuration |  | `dictionary` |  |
| &emsp;`enable` | Enable starting dropbear at system boot | `required` | `boolean` | `1` |
| &emsp;`verbose` | Enable verbose output by the start scrip | `optional` | `boolean` | `0` |
| &emsp;`BannerFile` | Name of a file to be printed before the user has authenticated successfully | `optional` | `string` | `7` |
| &emsp;`PasswordAuth` | Allow authenticating with passwords | `optional` | `boolean` | `1` |
| &emsp;`Port` | SSH service listening port | `required` | `integer` | `22` |
| &emsp;`RootPasswordAuth` | Allow authenticating as root with passwords | `optional` | `boolean` | `1` |
| &emsp;`RootLogin` | Allow SSH logins as root | `optional` | `boolean` | `1` |
| &emsp;`GatewayPorts` | Allow remote hosts to connect to forwarded port | `optional` | `boolean` | `0` |
| &emsp;`Interface` | Limit connections to specified network interface | `optional` | `string` | `lan` |
| &emsp;`keyfile` | Path to host key file | `optional` | `string` | `/etc/dropbear/authorized_keys` |
| &emsp;`SSHKeepAlive` | Keep alive | `optional` | `integer` | `300` |
| &emsp;`IdleTimeout` | Idle timeout | `optional` | `integer` | `0` |
| &emsp;`mdns` | Enable announcing the service via mDNS | `optional` | `boolean` | `1` |
| &emsp;`MaxAuthTries` | Amount of password entering retries before SSH server closes the connection | `optional` | `integer` | `3` |
| &emsp;`RecvWindowSize` | Per-channel receive window buffer size | `optional` | `integer` | `24576` |

> Note: multiple public keys can be added to `authorized_keys` file in `files` directory

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.dropbear
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/base-system/dropbear
