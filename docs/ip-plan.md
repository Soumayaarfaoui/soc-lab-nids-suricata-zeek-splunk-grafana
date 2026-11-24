# IP Plan (TP Table 2)

| Machine | Interface (TP) | Address | Virtual network |
|---|---|---|---|
| Kali Linux | eth0 | 192.168.10.10/24 | Monitored (VMnet10) |
| Target server | eth0 | 192.168.10.20/24 | Monitored (VMnet10) |
| Target server | eth1 | 192.168.20.20/24 | SOC management (VMnet19) |
| NIDS sensor | eth0 | none (promiscuous) | Monitored (VMnet10) |
| NIDS sensor | eth1 | 192.168.20.11/24 | SOC management (VMnet19) |
| Splunk | eth0 | 192.168.20.30/24 | SOC management (VMnet19) |
| Grafana | eth0 | 192.168.20.40/24 | SOC management (VMnet19) |

VMnet19 is the name VMware assigned to the 192.168.20.0/24 network.
VMnet10 and VMnet19 have DHCP disabled by design, so static addresses are set with netplan.
note on  config :

## How addresses were configured

Ubuntu machines (Target, Sensor, Splunk, Grafana) use netplan (`/etc/netplan/*.yaml`).
Kali uses NetworkManager (`nmcli`).

### Kali, step 1: static address on the monitored network (host-only)
```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.10.10/24 \
  ipv4.never-default yes
```

### Kali, step 2: NAT adapter stays on DHCP (temporary internet)
```bash
sudo nmcli connection modify "Wired connection 2" ipv4.method auto
```

### Kali, step 3: activate the host-only connection
```bash
sudo nmcli connection up "Wired connection 1"
```
