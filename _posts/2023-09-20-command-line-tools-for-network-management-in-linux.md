---
layout: post
title: Command line tools for network management in Linux
categories: Toolbox
tags: ["Linux", "CLI", "Networking"]
---

There are a wide array of tools for network management in Linux. As a system administrator, you must to have these to for network configuring, maintaining, and troubleshooting.

## iproute2

The `iproute2` is collection of tools for controlling network inferface and other various aspects of networking utilties. The tool is named `ip` in command line.

To display the IP addresss of network inferfaces in your device using the following command:
```bash
ip addr # or shortened version: ip a
```

You can use the `ip` command to configure the IP address of a network interface:
```bash
ip addr add 192.168.100.1 dev eth0
```

To a new route rule to the rule table using the following command:
```bash
ip route add 192.168.100.0/24 dev eth0
```

## ss

`ss` is a command line tool to investigate socket statistics. There are various options to control the output information and format. However, the most common used options is:
```bash
ss -lpntu | grep LISTEN
```
where `-l` to display the listening sockets only, `-p` to show the process, `-n` to show the exact banwidth values, and `-t` and `-u` to display TCP and UPD packets respectively.

## nslookup

`nslookup` is a tool to query DNS records, which is useful to diagnose DNS related problems. To get the MX (Mail Exchange) records of a domain, you can use the following command:
```bash
nslookup -type=mx google.com
```


## nmap

Nmap, of course, is a power package that provides a number of features for discovering hosts and ports, diagnose network problems, and so on. We can't cover lots of functions here. One of the most common used recipes is:
```bash
nmap -sn '192.168.100.*'
```
to discover hosts in the internal network without port scan.
