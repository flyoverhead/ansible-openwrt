# Changelog

All notable changes to `flyoverhead.openwrt`.

## 2.0.0

First release since the collection was brought in line with the other
`flyoverhead` collections. It carries breaking variable renames, so read the
Changed section before upgrading.

### Added

- **node_exporter**: role for `prometheus-node-exporter-lua`, wiring the
  `listen_interface`, `listen_port` and `listen_ipv6` settings and reloading the
  service. It is now part of the test playbook and the collection README.
- `CHANGELOG.md`, and a `build_ignore` in `galaxy.yml` so the published tarball
  stops shipping the test harness, linter configs and the vendored
  `gekmihesg.openwrt` role.
- `requirements.yml` and `requirements.txt` at the collection root, declaring
  `ansible.utils >=2.5.0` (the `wireguard` role has always used `ipmath` without
  declaring it) and the `gekmihesg.openwrt` role.
- `Taskfile.yml` gained `install`, `lint` and `build` targets.
- **extroot**: `extroot_hotplug_extras_url` and `extroot_opkg_extras_url`. The
  two OpenWrt wiki URLs the role fetches and executes were hardcoded mid-task;
  they are now overridable defaults.

### Changed

- **Breaking — every list and dictionary variable now defaults to empty.**
  Previously they defaulted to a single entry with every field set to `""`,
  which the role then looped over: running a role without configuring it tried
  to create UCI sections with an empty name. Roles are now a no-op until
  configured. The field schemas that used to live in `defaults/main.yml` are
  documented in each role's `README.md`.
- **Breaking — variables renamed to carry their role prefix**, per
  `ansible-lint`'s `var-naming[no-role-prefix]`:

  | Old | New |
  | :--- | :--- |
  | `system` | `system_settings` |
  | `dropbear` | `dropbear_settings` |
  | `mesh_enabled` | `mesh11sd_enabled` |
  | `ath10k_ct_fix` | `batman_ath10k_ct_fix` / `mesh11sd_ath10k_ct_fix` |
  | `non_mesh_pkgs` | `batman_non_mesh_pkgs` / `mesh11sd_non_mesh_pkgs` |
  | `dnsmasq_full_required_version` | `pbr_dnsmasq_full_required_version` |

  `ath10k_ct_fix` and `non_mesh_pkgs` were previously shared between `batman`
  and `mesh11sd`; each role now has its own, so a host running one of the two
  mesh implementations no longer configures the other by accident. Internal
  registers and facts were renamed the same way, but those are not part of the
  public interface.
- `requires_ansible` moved from `>=2.4` to `>=2.16.0`, and every role's
  `min_ansible_version` from `2.13` to `2.16`.
- Declared platform is now OpenWrt 22.03 and 23.05. 24.10 replaced `opkg` with
  `apk` and is explicitly unsupported; see the README.
- `test/` renamed to `tests/`, matching the other collections. `tests/playbook.yml`
  now runs `mesh11sd` and `node_exporter` too — both are inert unless enabled.
- `tests/ansible.cfg` used `ansible_ssh_private_key_file`, which is an inventory
  variable and is not a valid `ansible.cfg` key, so the setting had no effect.
  It is now `private_key_file`.
- README rewritten to the structure shared with `flyoverhead.server` and
  `flyoverhead.k3s`. The ~400 lines of example variables it inlined are gone;
  they duplicated `tests/` and had already drifted out of sync. The README links
  to those files instead, and collects the destructive behaviours under Gotchas.
- `gekmihesg.openwrt` is no longer vendored under `roles/`. A third-party galaxy
  role does not belong among the collection's own roles — it was linted as if it
  were ours, and `gekmihesg.openwrt` is not a valid collection role name.
  `requirements.yml` now installs it into `.ansible/roles`, and
  `tests/ansible.cfg` picks it up from there. Run `task install` before the
  first play.
- Collection-wide quoting now follows the shared `.yamllint`: redundant quotes
  removed from 563 single-token scalars. Purely cosmetic — every file was
  verified to parse to identical data before and after. The shell commands that
  keep their quotes carry an inline `yamllint disable` with the reason.
- The `command` tasks carry inline `# noqa: inline-env-var` and
  `command-instead-of-module`. Both rules fire on the only idiom that works
  here: OpenWrt has no Python, so `gekmihesg.openwrt` rewrites module calls to
  shell, and its `openwrt_command` takes a `uses_shell` parameter that stock
  `ansible.builtin.command` does not define. The directives are inline rather
  than in `.ansible-lint` so they travel with the repository.

### Fixed

- **mesh11sd**: `tasks/ath10k.yml` called `ansible.bultin.command` — a typo for
  `ansible.builtin.command` — so the whole role failed as soon as
  `ath10k_ct_fix` was enabled. The same file never initialised
  `replace_ath10k_ct_driver`, leaving the block below it referencing an
  undefined variable, and was missing the `uses_shell: true` that its piped
  commands need.
- **wireless**: 802.11r fast roaming never got a mobility domain. The task
  generating it tested a bare `mobility_domain`, which is never defined —
  the setting is `item.mobility_domain`, per interface — so the condition was
  always false and `mobility_domain` was silently omitted from every AP.
- **batman, mesh11sd, wireless, extroot**: `changed_when` compared a registered
  result *dictionary* against `0` (`changed_when: reload_wifi_status != 0`),
  which is always true. The handlers now state `changed_when: true` directly,
  which is what that comparison amounted to.
- **batman**: `Check batman luci package status` greps for a package that is
  expected to be missing, but `grep` exits 1 when it matches nothing and the
  task had no `failed_when`, so the play aborted exactly when the LuCI package
  needed installing. `pbr` had the same problem checking the installed
  `dnsmasq-full` version, and `batman`/`mesh11sd` checking the ath10k firmware.
- **extroot**: `tasks/check.yml` dereferenced `extroot_status.result` in a
  `failed_when` whose left-hand side had just established the attribute was
  undefined, so a device with no fstab mount — the state extroot is meant to
  fix — raised an undefined-attribute error instead of proceeding. `uci get`
  reports "Entry not found" in `msg`, not `result`; both that check and the two
  equivalents in `wireguard` now read `msg`.
- **extroot**: `extroot_configure` was only ever set by `tasks/check.yml`, which
  runs behind `extroot_enabled`. With extroot disabled the variable stayed
  undefined and the guards in `tasks/main.yml` failed. It now defaults to
  `false`.
- **pbr**: dnsmasq versions were compared as strings, so `2.9` sorted above
  `2.89` and `2.100` below it. Both comparisons now use the `version` test.
- **pbr**: `tasks/snapshot.yml` proceeded to download and install even when the
  package index returned no match, producing `curl`/`opkg` calls against an
  empty filename. It now skips, and removes the downloaded file with
  `ansible.builtin.file` rather than `rm`.
- **wireguard**: freshly generated private, public and preshared keys were
  printed in task output. Those tasks are now `no_log: true`.
- **batman**: the uploaded `luci-proto-batman-adv.ipk` was written mode `0755`
  and left in `/tmp`. It is now `0644` and removed after installation.
- Missing `changed_when` on the `command` tasks in `extroot`, `pbr`,
  `wireguard`, `wireless` and `batman`, all of which reported `changed` on every
  run — including the read-only probes, which now report `changed_when: false`.

## 1.0.0

Initial release: the `batman`, `dhcp`, `dropbear`, `extroot`, `firewall`,
`mesh11sd`, `network`, `pbr`, `system`, `wireguard` and `wireless` roles.
