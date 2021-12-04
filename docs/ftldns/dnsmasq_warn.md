# Known `dnsmasq` warnings

Warnings commonly seen in `dnsmasq`'s log file (`/var/log/pihole.log`) and the Pi-hole diagnosis system.

> **ignoring zone transfer request from `ADDRESS`**

Zone transfer requests (AXFR) are refused *unless* `auth-sec-servers` or `auth-peers` is set. The address requesting the AXFR is logged.

> **DHCP request for unsupported hardware type (`X`) received on `Y`**

`dnsmasq` only supports Ethernet on *BSD. The integer `X` describes the hardware type (see `/usr/include/linux/if_arp.h` for definitions). `Y` is the name of the receiving interface.

> **Unknown protocol version from route socket**

As the warning says. No action is performed in this case.

> **No IPv4 address found for `HOSTNAME`**

Lookup for an A record in the cache returned no result.

> **`HOSTNAME` is a CNAME, not giving it to the DHCP lease of `ADDRESS`**

A hostname claimed by a DHCP client is a known CNAME. `dnsmasq` does not allow the DHCP clients to take this name.

> **not giving name `HOSTNAME` to the DHCP lease of `ADDRESS` because the name exists in `SOURCE` with address `CACHE_ADDR`**

If `HOSTNAME` is known through a HOSTS file or config (see `SOURCE`) and the DHCP address `ADDRESS` does *not* match the address in the cache (`CACHE_ADDR`), `dnsmasq` prevents giving the name to a DHCP client. This prevents possible hostname hijacking by malicious devices.

> **unknown interface `IF_NAME` in bridge-interface**

If the interface on which the DHCPv6 request was received is an alias of some other interface (as specified by the `bridge-interface` option), `dnsmasq` needs to look for DHCPv6 contexts associated with the aliased interface instead of with the aliasing one. This warning complains that the referenced interface does not exist.

> **DHCP packet received on `IF_NAME` which has no address**

No DHCP context has been configured for this interface. Check your DHCP settings.

> **Error sending DHCP packet to `ADDRESS`: `MSG`**

This can fail when, e.g., `iptables` `DROPS` destination `255.255.255.255`. Check your firewall settings.

> **DHCP range `ADDRESS_FROM` -- `ADDRESS_TO` is not consistent with netmask `SUBNET_MASK`**

This warning highlights that one of the two addresses is outside of the configured subnet mask. As a consequence, not all addresses may be reachable from configured hosts leading to unexpected behavior on the clients. Make your DHCP settings consistent.

> **Ignoring duplicate dhcp-option `OPTNUM`**

DHCP options specified more than once are ignored. The corresponding option ID is given by `OPTNUM`

> **`HOSTNAME` has more than one address in hostsfile, using `ADDRESS` for DHCP**

Some people like to keep all static IP addresses in `/etc/hosts`. `dnsmasq` goes through `/etc/hosts` and sets static addresses for any DHCP config records which don't have an address and whose name matches where `dnsmasq` maintains the invariant that any IP address can appear in at least one DHCP host.

> **duplicate IP address `ADDRESS` (`HOSTNAME`) in dhcp-config directive**

As the warning says.

> **cache size greater than 10000 may cause performance issues, and is unlikely to be useful.**

This causes the cache to consume a lot on memory and slows down cache lookups. As expiring cache entries naturally make room for new records, a large cache does not give any advantages beyond a certain level. This level is typically not very high. Try reducing the cache. Watch out for cache-evictions. If they are zero over a long time, your cache is larger than what you need.

> **warning: failed to change owner of `PIDFILE`: `MSG`**

Changing the ownership of the PID file (`PIDFILE`) to the user `dnsmasq` will be running as failed. A descriptive error message (`MSG`) is given to explain why the `chown` failed.

> **setting --bind-interfaces option because of OS limitations**

Only affects non-Linux builds. `bind-dynamic` is not supported on non-Linux. `dnsmasq` falls back to `bind-interfaces`

> **warning: interface `NAME` does not currently exist**

As the warning says. Likely caused by an `interface=NAME` option where the interface `NAME` does not exist. Check if your operating system may have changed from simple (like `eth0`) to predictable (like `enp2s0`) interface names. Update your configuration accordingly.

> **warning: ignoring resolv-file flag because no-resolv is set**

This points to a conflicting configuration that may not behave as expected. Remove either the `resolv-file` or the `--no-resolv` option.

> **warning: no upstream servers configured**

Only local names can be answered as no `server=...` lines are defined.

> **warning: `PATH` inaccessible**

The TFTP prefix (set by `tftp-prefix`) is inaccessible or not a directory.

> **warning: TFTP directory `PATH` inaccessible**

One of the defined TFTP prefix (comma-separated arguments of `tftp-prefix`) is inaccessible or not a directory.

> **restricting maximum simultaneous TFTP transfers to `NUMBER`**

If a limited range of ports is in use, this also limits the number of concurrent TFTP transfers.

> **script process killed by signal `SIGNUM`**

A script helper was killed by an external signal (`SIGNUM`).

> **script process exited with status `EXITCODE`**

A script helper exited with a non-success return code (`EXITCODE`).

> **failed to access `RESOLV_FILE`: `MSG`**

This line is logged when `dnsmasq` fails to access one of the files defined through `resolv-file`. This warning is printed only once per file.

> **no servers found in `RESOLV_FILE`, will retry**

The read file was empty. `dnsmasq` will read it again. This warning is printed only once per file.

> **Insecure DS reply received for `DOMAIN`, check domain configuration and upstream DNS server DNSSEC support**

A query was marked BOGUS because a DS query could not be validated (returned INSECURE).

> **discarding DNS reply: subnet option mismatch**

When the EDNS0 option `add-subnet` is in use, `dnsmasq` needs to check the reply. If the returned subnet does not match, the reply is treated as invalid.

> **nameserver `ADDRESS` refused to do a recursive query**

Upstream at address `ADDRESS` is missing the `RA` (recursion available) bit. This warning is printed only once per server.

> **possible DNS-rebind attack detected: `NAME`**

`A` and `AAAA` answers are checked against possible rebind attacks when `stop-dns-rebind` is enabled. See `rebind-domain-ok=/domain/` for adding exceptions.

> **reducing DNS packet size for nameserver `ADDRESS` to `SAFE_PKTSZ`**

When receiving answers from upstream only with a smaller maximum DNS packet size, `dnsmasq` remembers this decision and makes it permanent in the current session.

> **Ignoring query from non-local network**

`dnsmasq` can be configured to only accept queries from at-most-one-hop-away addresses using the option `local-service`. Other queries are discarded in this case. This is ment to be a safe default to keep otherwise unconfigured installations safe. Note that `local-service` is ignored if *any* access-control config is in place (`interface`, `except-interface`, `listen-address` or `auth-server`).

> **Maximum number of concurrent DNS queries reached (max: `NUMBER`)**

The configured maximum number of concurrent DNS queries has been reached. The system is either very busy at the moment or not receiving queries from the configured upstream. Check your connectivity or the upstream DNS server status. The warning can also be printed when being spammed with an excessive amount of duplicates. It is printed at most once every five seconds.

> **Maximum number of concurrent DNS queries to `DOMAIN` reached (max: `NUMBER`)**

Same as above but for a specific domain.

> **ignoring invalid line in lease database: `STRING` `STRING` `STRING` `STRING` ...**

An invalid line in the lease file has been skipped.

> **ignoring invalid line in lease database, bad address: `ADDRESS`**

Address found in the lease file is neither a valid IPv4 nor a valid IPv6 address. The line is skipped.

> **Ignoring domain `CONFIG_DOMAIN` for DHCP host name `HOSTNAME`**

A DHCP client is not allowed to claim name `HOSTNAME` in the current DHCP configuration.

> **overflow: `NUMBER` log entries lost**

When using asynchroneous logging and the disk is too slow, we can loose log lines during busy times. This can be avoided by decreasing the system load or switching to synchroneous logging. Note that synchroneous logging has the disadvantage of blocking DNS resolution when waiting for the log to be written to disk.

> **failed to create listening socket for `ADDRESS`: `MSG`**

A failure to bind addresses given by `listen-address` is accepted when `dnsmasq` is configured with `bind-dynamic`.

> **failed to create listening socket for port `NUMBER`: `MSG`**

Same as above but for a port rather than an address.

> **LOUD WARNING: listening on `ADDRESSS` may accept requests via interfaces other than `IFNAME`**

When using `bind-interfaces`, the only access control is the addresses `dnsmasq` is listening on. There's nothing to avoid a query to the address of an internal interface arriving via an external interface where we don't want to accept queries, except that in the usual case the addresses of internal interfaces are RFC1918. When `bind-interfaces` in use, and we listen on an address that looks like it's probably globally routeable, this warning is printed.

> **LOUD WARNING: use --bind-dynamic rather than --bind-interfaces to avoid DNS amplification attacks via these interface(s)**

Advise printed when above's warning is printed. This warning is printed only once.

> **warning: using interface `IF_NAME` instead**

When configuring an interface alias (like `eth0:0`), `dnsmasq` will be listening on the physical (e.g. `eth0`) interface, instead.

> **warning: no addresses found for interface `IF_NAME`**

`dnsmasq` has been configured to listen on an interface that has no address assigned to it.

> **ignoring nameserver `ADDRESS` - local interface**

At least one `server` directive is redundant and point to the `dnsmasq` instance itself. The server is ignored.

> **ignoring nameserver `ADDRESS` - cannot make/bind socket: `MSG`**

`dnsmasq` failed to allocate a socket for the mentioned server. The server is ignored.

> **no address range available for DHCP request with subnet selector `SUBNET`**

No DHCP context has been configured for this subnet selector. Check your DHCP settings.

> **no address range available for DHCP request via `ADDRESS`**

No DHCP context has been configured for this address. Check your DHCP settings.

> **no address range available for DHCP request via `IF_NAME`**

No DHCP context has been configured for this interface. Check your DHCP settings.

> **disabling DHCP static address `ADDRESS` for `HOSTNAME`**

Static DHCP leases are disabled when sending a DHCPDECLINE packet.

> **not using configured address `ADDRESS` because it is leased to `MAC`**

DHCPDISCOVER: Not handing out configured address because it is already actively used to anohter device with hardware address `MAC`.

> **not using configured address `ADDRESS` because it is in use by the server or relay**

Handing out addresses used by known critical infrastructure (like the DHCP server or a relay) is prevented to avoid IP address duplication issues.

> **not using configured address `ADDRESS` because it was previously declined**

As the warning says. Check your log file for reasons of a prior refusal to hand out this lease. This warning is at most logged once every 10 minutes for a given address.

> **cannot send DHCP/BOOTP option `NUMBER`: no space left in packet**

Use less DHCP options as the space for options is limited and cannot be extended (RFC2131).

> **cannot send RFC3925 option: too many options for enterprise number `NUMBER`**

A maximum packet length of 250 bytes has to be ensured for `dhcp-option = vi-encap:13,17,...` configurations.

> **no address range available for DHCPv6 request from relay at `ADDRESS`**

No DHCPv6 context has been configured for this address. Check your DHCPv6 settings.

> **no address range available for DHCPv6 request via `IF_NAME`**

No DHCPv6 context has been configured for this interface. Check your DHCPv6 settings.

> **disabling DHCP static address `ADDRESS` for `TIME`**

Static DHCPv6 leases are disabled when sending a DHCP(6)DECLINE packet.

> **IPset: error: `MSG`**

A non-critical error was encountered when trying to access an IPset device. A human-readable message explains it further.

> **warning: DIOCRADDADDRS: `MSG`**

A non-critical error was encountered when trying to add an address to an existing `ipset`. A human-readable message explains it further.

> **warning: DIOCRDELADDRS: `MSG`**

A non-critical error was encountered when trying to remove an address from an existing `ipset`. A human-readable message explains it further.