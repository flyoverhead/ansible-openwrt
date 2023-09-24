# `flyoverhead.openwrt.dhcp`

OpenWRT `dhcp` configuration
- configure dnsmasq settings
- create/delete/configure dhcp pools
- create/delete/configure static leases
- create/delete/configure ipsets

## Role Variables

| Variable | Descritpion | Status | Type | Example |
| :--- | :--- | :--- | :--- | :--- |
| `dhcp_common` | DHCP and DNS [common options](https://openwrt.org/docs/guide-user/base-system/dhcp#common_options) |  | `dictionary` |  |
| &emsp;`authoritative` | Force dnsmasq into authoritative mode (used if this is the only server on the network) | `required` | `boolean` | `1` |
| &emsp;`boguspriv` | Reject reverse lookups to private IP ranges where no corresponding entry exists in `/etc/hosts` | `optional` | `boolean` | `1` |
| &emsp;`cachesize` | Size of dnsmasq query cache | `optional` | `integer` | `150` |
| &emsp;`domain` | Local domain suffix appended to DHCP names and hosts file entries | `optional` | `string` | `lan` |
| &emsp;`domainneeded` | Never forward queries for plain names, without dots or domain parts, to upstream nameservers | `optional` | `boolean` | `1` |
| &emsp;`dnssec` | Validate DNS replies and cache DNSSEC data | `optional` | `boolean` | `0` |
| &emsp;`dnsseccheckunsigned` | Check the zones of unsigned replies to ensure that unsigned replies are allowed in those zones | `optional` | `boolean` | `0` |
| &emsp;`expandhosts` | Add the local domain part to names found in /etc/hosts | `optional` | `boolean` | `1` |
| &emsp;`filterwin2k` | Do not forward requests that cannot be answered by public name servers | `optional` | `boolean` | `0` |
| &emsp;`fqdn` | Do not resolve unqualifed local hostnames (needs `domain` to be set) | `optional` | `boolean` | `0` |
| &emsp;`leasefile` | Path to file to store DHCP leases in | `optional` | `string` | `/tmp/dhcp.leases` |
| &emsp;`local` | Never forward matching domains and subdomains, resolve from DHCP or hosts files only. | `optional` | `string` | `/lan/` |
| &emsp;`localise_queries` | Return answers to DNS queries matching the subnet from which the query was received if multiple IPs are available | `optional` | `boolean` | `1` |
| &emsp;`localservice` | Accept DNS queries only from hosts whose address is on a local subnet | `optional` | `boolean` | `0` |
| &emsp;`nonegcache` | Do not cache negative replies, e.g. for non-existent domains | `optional` | `boolean` | `1` |
| &emsp;`nonwildcard` | Bind dynamically to interfaces rather than wildcard address | `optional` | `boolean` | `1` |
| &emsp;`readethers` | Read static lease entries from /etc/ethers | `optional` | `boolean` | `1` |
| &emsp;`rebind_protection` | Enables DNS rebind attack protection by discarding upstream RFC1918 responses | `optional` | `boolean` | `1` |
| &emsp;`rebind_localhost` | Allows upstream 127.0.0.0/8 responses, required for DNS based blacklist services (needs `rebind_protection` to be enabled) | `optional` | `boolean` | `1` |
| &emsp;`rebind_domain` | List of domains to allow RFC1918 responses for (needs `rebind_protection` to be enabled) | `optional` | `list` | `["/example.com/", "/maydomain.com/"]` |
| &emsp;`resolvfile` | Path to file with upstream resolvers | `optional` | `string` | `"/tmp/resolv.conf.d/resolv.conf.auto` |
| &emsp;`server` | List of DNS upstream servers to forward requests to | `optional` | `list` | `["192.168.1.1", "192.168.1.2"]` |
| &emsp;`serverlist` | Path to file with DNS upstream servers list content | `optional` | `string` | `/etc/dnsmasq.servers` |
| &emsp;`address` | List of IP addresses for queried domains | `optional` | `list` | `/site.example.com/192.168.1.1` |
| &emsp;`allservers` | Force dnsmasq to send all queries to all available upstream DNS servers | `optional` | `boolean` | `0` |
| `dhcp_pools` | List of per interface [lease pools and settings](https://openwrt.org/docs/guide-user/base-system/dhcp#dhcp_pools) |  | `list of dictionaries` |  |
| &emsp;`id` | Unique dhcp pool ID | `mandatory` | `string` | `lan` |
| &emsp;`interface` | Interface associated with DHCP pool (must be one of the interfaces defined in `/etc/config/network`) | `mandatory` | `string` | `lan` |
| &emsp;`state` | DHCP pool status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`dhcp_option` | List of DHCP options | `optional` | `list` | `["3,192.168.1.1", "6,192.168.1.1"]` |
| &emsp;`force` | Force DHCP serving on the specified interface even if another DHCP server is detected on the same network segment | `optional` | `boolean` | `0` |
| &emsp;`dhcpv4` | Enable or disable DHCPv4 server (`server` or `disabled`) | `mandatory` | `string` | `server` |
| &emsp;`leasetime` | Lease time of addresses handed out to client | `required` | `string` | `12h` |
| &emsp;`limit` | Size of the address pool | `required` | `integer` | `150` |
| &emsp;`start` | Offset from the network address of the underlying interface (for calculating the minimum address that may be leased to clients) | `required` | `integer` | `100` |
| &emsp;`ra` | Operation mode of the Router Advertisements service (`server`, `relay`, `hybrid` or `disabled`) | `optional` | `string` | `disabled` |
| &emsp;`dhcpv6` | Operation mode of the DHCPv6 service (`server`, `relay`, `hybrid` or `disabled`) | `optional` | `string` | `disabled` |
| &emsp;`dns_service` | Enable local IPv6 DNS server | `optional` | `boolean` | `0` |
| `dhcp_leases` | List of hosts' [static leases](https://openwrt.org/docs/guide-user/base-system/dhcp#static_leases) |  | `list of dictionaries` |  |
| &emsp;`id` | Unique dhcp lease ID | `optional` | `string` | `host01` |
| &emsp;`name` | Optional hostname to assign to the host | `optional` | `string` | `host01` |
| &emsp;`state` | Static lease status (`present` or `absent`) | `required` | `string` | `present` |
| &emsp;`ip` | IP address to assign to the host (`IP address` or `ignore`) | `mandatory` | `string` | `192.168.1.11` |
| &emsp;`mac` | Hardware address of the host | `mandatory` | `string` | `00:11:22:33:44:55` |

## Dependencies

| Name | Description |
| :--- | :--- |
| `Ansible Role: openwrt` | [Ansible role by gekmihesg](https://github.com/gekmihesg/ansible-openwrt) for managing OpenWRT and derivatives |

## Example Playbook

```yaml
- hosts: openwrt
  roles:
      - role: flyoverhead.openwrt.dhcp
```

## Example Vars

```yaml
dhcp_common:
  authoritative: "1"
  boguspriv: "1"
  cachesize: "1000"
  domainneeded: "1"
  rebind_protection: "1"
  rebind_localhost: "1"
dhcp_pools:
  - id: "lan"
    interface: "lan"
    state: "state"
    dhcpv4: "server"
    limit: "50"
    start: "50"
dhcp_leases:
  - id: "host01"
    name: "host01"
    state: "present"
    ip: "192.168.1.51"
    mac: "00:11:22:33:44:55"
```

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.txt)

## Author Information

fly0v3rH34D

## References

- https://openwrt.org/docs/guide-user/base-system/dhcp
