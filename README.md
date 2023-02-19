# dae

<img src="https://github.com/v2rayA/dae/blob/main/logo.png" border="0" width="25%">

**_dae_**, means goose, is a lightweight and high-performance transparent proxy solution.

In order to improve the traffic split performance as much as possible, dae runs the transparent proxy and traffic split suite in the linux kernel by eBPF. Therefore, we have the opportunity to make the direct traffic bypass the forwarding by proxy application and achieve true direct traffic through. Under such a magic trick, there is almost no performance loss and additional resource consumption for direct traffic.

As a successor of [v2rayA](https://github.com/v2rayA/v2rayA), dae abandoned v2ray-core to meet the needs of users more freely.

**Features**

1. Implement `Real direct` traffic split (need ipforward on) to achieve [high performance](https://docs.google.com/spreadsheets/d/1UaWU6nNho7edBNjNqC8dfGXLlW0-cm84MM7sH6Gp7UE/edit?usp=sharing).
1. Support to split traffic by process name in local host.
1. Support to split traffic by MAC address in LAN.
1. Support to split traffic with invert match rules.
1. Support to automatically switch nodes according to policy. That is to say, support to automatically test independent TCP/UDP/IPv4/IPv6 latencies, and then use the best nodes for corresponding traffic according to user-defined policy.
1. Support full-cone NAT for shadowsocks, vmess, socks5 and trojan(-go).

## Prerequisites

### Kernel Version

Use `uname -r` to check the kernel version on your machine.

**Bind to LAN: >= 5.8**

You need bind dae to LAN interface, if you want to provide network service for LAN as an intermediate device.

This feature requires the kernel version of machine on which dae install >= 5.8.

Note that if you bind dae to LAN only, dae only provide network service for traffic from LAN, and not impact local programs.

**Bind to WAN: >= 5.8**

You need bind dae to WAN interface, if you want dae to provide network service for local programs.

This feature requires kernel version of the machine >= 5.8.

Note that if you bind dae to WAN only, dae only provide network service for local programs and not impact traffic coming in from other interfaces.

### Kernel Configuration Item

Usually, mainstream desktop distributions have these items turned on. But in order to reduce kernel size, some items are turned off by default on embedded device distributions like OpenWRT, Armbian, etc.

Use following command to show kernel configuration items on your machine.

```shell
zcat /proc/config.gz || cat /boot/{config,config-$(uname -r)}
```

**Bind to LAN**

```
CONFIG_DEBUG_INFO_BTF
```

**Bind to WAN**:

```
CONFIG_DEBUG_INFO_BTF
```

Check them using command like:

```shell
(zcat /proc/config.gz || cat /boot/{config,config-$(uname -r)}) | grep 'CONFIG_DEBUG_INFO_BTF='
```

### Enable IP Forwarding

By default, any latest Linux distributions will have IP Forwarding `disabled`. In the case where we need to up a Linux router/gateway or a VPN server or simply a plain dial-in server, then we must need to enable forwarding. Do the followings to have `ip-forwarding` feature enabled:

```shell
sudo tee /etc/sysctl.d/dae.conf<<EOF
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
EOF
sudo sysctl --system
```

## Getting Started

Please refer to [Quick Start Guide](./docs/getting-started/README.md) to start using `Dae` right away!

## TODO

- [ ] Check dns upstream and source loop (whether upstream is also a client of us) and remind the user to add sip rule.
- [ ] WAN L4Checksum problem.
- [ ] If the NIC checksumming offload is enabled, the Linux network stack will make a simple checksum a packet when it is sent out from local. When NIC discovers that the source IP of the packet is the local IP of the NIC, it will checksum it complete this checksum.
- [ ] But the problem is, after the Linux network stack, before entering the network card, we modify the source IP of this packet, causing the Linux network stack to only make a simple checksum, and the NIC also assumes that this packet is not sent from local, so no further checksum completing.
- [ ] MACv2 extension extraction.
- [ ] Log to userspace.
- [ ] Protocol-oriented node features detecting (or filter), such as full-cone (especially VMess and VLESS).
- [ ] DNS traffic split.
- [ ] Add quick-start guide
- [ ] ...
