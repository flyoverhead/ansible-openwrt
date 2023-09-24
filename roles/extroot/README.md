# `flyoverhead.openwrt.extroot`

OpenWRT `extroot` configuration
- configure extroot

## Role Variables

| Variable | Descritpion | Status | Type | Default/Example |
| :--- | :--- | :--- | :--- | :--- |
| `extroot_enabled` | Enable extroot configuration | `required` | `boolean` | `false` |
| `extroot_pkgs` | List of packages required for extroot configuration | `required` | `list` | `["kmod-usb-core", "kmod-usb-storage", "kmod-usb2", "kmod-usb3", "block-mount", "kmod-fs-ext4", "e2fsprogs", "parted"]` |
| `extroot_device` | External USB device name | `required` | `string` | `sda` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.extroot
```

## Example Vars

```yaml
extroot_enabled: true
extroot_device: "sda"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/additional-software/extroot_configuration
- https://openwrt.org/docs/guide-user/advanced/hotplug_extras
- https://openwrt.org/docs/guide-user/advanced/opkg_extras
