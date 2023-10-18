---
title: "How I build development environment for web developers using Proxmox and Dedicated Server"
date: 2023-10-18T07:02:14Z
draft: false
toc: false
images:
tags:
- development
- proxmox
- linux
- web
- startup
- pfsense
---

## Introduction

Welcome to my journey of creating a stable and reliable development environment for web developers. This environment is built upon a dedicated server running Proxmox. Our primary goals were to provide each developer with an isolated and accessible development environment both from the Internet and the local network.

## Setup

### Dedicated Server

We chose a dedicated server from OVH with the following specifications:

- CPU: Intel Xeon E3-1270v6 (4 cores, 8 threads, 3.8GHz base, 4.2GHz turbo)
- RAM: 64GB DDR4 ECC at 2400 MHz
- Disk: SoftRaid with 2x450GB NVMe drives
- Network: 1Gbps
- IPs: 2 IPv4 (main and failover)

### Network Configuration

The server has two IP addresses: one public main IP and a second failover IP used for the pfSense router. 
The pfSense router manages private IP addresses for virtual machines and LXC containers.

### Domain Name

We selected a single domain name for the project, configuring a DNS wildcard A record (*.domain) for the server's failover IP. 
This domain name allows us to access development environments from the Internet.

### pfSense

pfSense, a free and open-source firewall and router, runs as a virtual machine on Proxmox. 
It uses the failover IP as the WAN interface and distributes private IP addresses to virtual machines and LXC containers via the LAN interface. 
PfSense also serves as a DHCP server, pushing the DNS server address (pfSense's IP) to clients. 
The router forwards HTTP (80) and HTTPS (443) ports to a virtual machine running a Nginx reverse proxy.

### OpenVPN

OpenVPN is integrated into the pfSense router. Each developer has a dedicated group with limited access, 
enabling them to connect to the Proxmox development network from anywhere. From the local network, 
developers can reach all LXC containers and virtual machines.

### Nginx Reverse Proxy

Nginx reverse proxy, installed as a virtual machine, listens on ports 80 and 443. 
It has a LetsEncrypt certificate for the domain name with wildcard. Nginx forwards requests based on the domain name's suffix 
- `dev1.domain` -> LXC/container with `dev1` hostname

### MySQL Database

The primary MySQL database, also a virtual machine, listens on port 3306 and uses a private IP address. 
It connects through the pfSense router, serving LXC/container development environments. 
The database is accessible via its hostname from within LXC/containers.

### LXC/Containers

Each developer has their dedicated LXC/container, configured with a private IP address and access through the pfSense router.
Nginx must be installed to listen on port 80 because requests are forwarded by the Nginx reverse proxy based on the domain name suffix.

## Summary

This setup is simple, cost-effective, and highly flexible.
It is isolated and secure, with LXC/containers protected from the Internet and other local network resources. 
It offers a convenient and scalable environment for web development.

Feel free to contact me at [d8x[at]d8x.me](mailto:d8x@d8x.me) for more details or inquiries.



