# Windows Network Troubleshooting & Maintenance Guide

A reference for diagnosing connectivity drops, isolating latency bottlenecks, auditing
active connections, managing Wi-Fi profiles, and performing full network stack resets using
built-in Windows tools.

> **Prerequisites:** Several commands below alter system configuration and require an
> **Elevated Command Prompt (Run as Administrator).**
>
> Quick launch: press **Win + R**, type `cmd`, then press **Ctrl + Shift + Enter**.

---

## Table of Contents

1. [Network Configuration Snapshot (ipconfig)](#1-network-configuration-snapshot-ipconfig)
2. [Connectivity & Latency Testing (ping & pathping)](#2-connectivity--latency-testing-ping--pathping)
3. [DNS Verification (nslookup)](#3-dns-verification-nslookup)
4. [Route Mapping & Bottleneck Isolation (tracert)](#4-route-mapping--bottleneck-isolation-tracert)
5. [Active Connections & Device Mapping (netstat & arp)](#5-active-connections--device-mapping-netstat--arp)
6. [Wi-Fi Profile & Password Management (netsh wlan)](#6-wi-fi-profile--password-management-netsh-wlan)
7. [Full Network Stack Reset](#7-full-network-stack-reset)

---

## 1. Network Configuration Snapshot (ipconfig)

### Basic Interface Overview

```cmd
ipconfig
```

Shows all active network interfaces. Key values to note:

- **IPv4 Address** — your local machine IP (typically `192.168.x.x` or `10.x.x.x`)
- **Default Gateway** — your router's local IP; paste this into a browser to access the
  router's admin panel
- **Subnet Mask** — defines the local network boundary (standard home: `255.255.255.0`)

### Full Details (MAC, DHCP, DNS)

```cmd
ipconfig /all
```

Adds hardware (MAC) address, DHCP lease expiry, and upstream DNS server assignments.

### Release and Renew IP Lease

```cmd
ipconfig /release
ipconfig /renew
```

Drops the current router-assigned IP and requests a fresh one. Resolves "Connected but no
internet" errors caused by stale IP conflicts.

### Flush DNS Cache

```cmd
ipconfig /flushdns
```

Clears the local DNS resolver database. Use this when a site that changed servers refuses
to load, or after editing `hosts` file entries.

---

## 2. Connectivity & Latency Testing (ping & pathping)

### Basic Reachability Test

```cmd
ping google.com
```

- `Reply from [IP]: time=Xms` — successful link; the `time` value is your round-trip latency.
- `Request timed out` — the target is unreachable, offline, or blocking ICMP traffic.

### Triage Routine — Internet vs DNS

Run these two commands in order when you can't browse the web:

```cmd
ping 8.8.8.8       # Direct IP — tests raw internet connectivity
ping google.com    # Domain name — tests DNS resolution
```

> If direct IP works but domain name times out, your internet connection is fine — the
> problem is entirely with DNS configuration.

### Continuous Monitoring

```cmd
ping -t google.com
```

Runs an infinite ping stream for tracking live dropouts or latency spikes. Press **Ctrl + C**
to stop and display aggregate statistics.

### Packet Loss Analysis (pathping)

```cmd
pathping google.com
```

Combines route tracing with per-hop packet loss analysis. Sends 100 packets to each
intermediate router along the path to calculate precise loss percentages. Takes several
minutes — not for quick checks, but for diagnosing intermittent drops that standard `ping`
misses.

---

## 3. DNS Verification (nslookup)

### Query Current DNS Server

```cmd
nslookup google.com
```

- **Server / Address** — the upstream DNS server your machine is currently using
- **Non-authoritative answer** — the resolved IP addresses for that domain

### Test Against a Specific DNS Provider

```cmd
nslookup google.com 8.8.8.8
```

Forces resolution through Google's public DNS (`8.8.8.8`) instead of your default config.

> If this resolves correctly but `nslookup google.com` (without specifying a server) fails,
> your ISP's DNS servers are the problem. Switch your network adapter's DNS to `8.8.8.8`
> (Google) or `1.1.1.1` (Cloudflare) in **Network Adapter Settings → IPv4 Properties**.

---

## 4. Route Mapping & Bottleneck Isolation (tracert)

### Trace the Route to a Destination

```cmd
tracert google.com
```

Each numbered row is one hop (router) along the path. Three millisecond values show
individual transit trials to that hop.

**Reading the output:**

| Pattern                                | Implication                                       |
| :------------------------------------- | :------------------------------------------------ |
| Hop 1 times out                        | Issue is in your local network or router          |
| Hops 1–2 solid, then timeouts          | Your local link is healthy; outage is at your ISP |
| Latency spikes at hop N and stays high | Bottleneck is at that specific router             |

### Fast Route Mapping (Skip DNS Resolution)

```cmd
tracert /d google.com
```

Displays raw IPs instead of resolving hostnames per hop — significantly faster output.

---

## 5. Active Connections & Device Mapping (netstat & arp)

### All Active Connections and Listening Ports

```cmd
netstat -an
```

- `-a` — all active connections and listening ports
- `-n` — numeric addresses (no DNS resolution, faster output)

**States to monitor:**

- `ESTABLISHED` — active open connection
- `LISTENING` — local port open and waiting for inbound connections

### Map Connections to Executable Names

```cmd
netstat -b
```

Injects the executable name (e.g. `chrome.exe`) below each connection block. Requires
elevation. Effective for identifying apps making unexpected outbound connections.

### Combined Output (Numeric + PID + Executable)

```cmd
netstat -nob
```

Best for security audits — shows remote IPs, ports, and the exact process responsible.

### Local Device ARP Cache

```cmd
arp -a
```

Lists the IP-to-MAC mapping of every device that has recently communicated with your machine.

> **Security note:** If two distinct local IPs map to the same MAC address, the network
> may be under an ARP spoofing (man-in-the-middle) attack.

---

## 6. Wi-Fi Profile & Password Management (netsh wlan)

### List All Saved Wi-Fi Networks

```cmd
netsh wlan show profiles
```

Shows every Wi-Fi network the device has ever connected to and stored credentials for.

### Retrieve a Saved Password in Plain Text

```cmd
netsh wlan show profile name="NETWORK_NAME" key=clear
```

Replace `NETWORK_NAME` with the exact profile name from the list above. Look under
**Security settings → Key Content** for the password in plain text.

### Delete a Saved Network Profile

```cmd
netsh wlan delete profile name="NETWORK_NAME"
```

Useful for clearing a corrupted profile or forcing the OS to forget an old password so you
can re-enter updated credentials.

---

## 7. Full Network Stack Reset

When standard fixes (router restart, driver reinstall) don't resolve the problem, this
sequence rebuilds the entire Windows network stack from scratch.

Open an **Elevated Command Prompt** and run these commands in order:

```cmd
ipconfig /flushdns
ipconfig /release
ipconfig /renew
netsh winsock reset
netsh int ip reset
netsh int tcp reset
netsh advfirewall reset
```

### What Each Step Does

| Command                        | Effect                                                                                                        |
| :----------------------------- | :------------------------------------------------------------------------------------------------------------ |
| `ipconfig /flushdns`           | Clears the local DNS resolver cache                                                                           |
| `ipconfig /release` + `/renew` | Forces a clean IP lease from the router                                                                       |
| `netsh winsock reset`          | Rebuilds the Windows Socket API catalog — fixes apps that can't load web content despite an active connection |
| `netsh int ip reset`           | Resets the TCP/IP protocol stack configuration                                                                |
| `netsh int tcp reset`          | Resets TCP-specific settings                                                                                  |
| `netsh advfirewall reset`      | Clears all custom Windows Defender Firewall rules back to factory defaults                                    |

> **Restart your computer immediately after running this sequence.** Custom firewall rules
> and saved network preferences will be cleared — reconfigure them after the reboot if needed.

---

**After rebooting, the Windows network stack will be in a clean factory state.**
