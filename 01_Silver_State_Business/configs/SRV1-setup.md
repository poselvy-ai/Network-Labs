
# SRV1 — DNS + Web (Ubuntu in CML)

SRV1 is an **Ubuntu** node on HQSW2 E0/2 (VLAN 50), address `10.10.50.10/24`, gateway `10.10.50.1`.

## 1. Get packages installed (one time)

CML nodes have no internet by default, so `apt` will fail. Temporarily:

1. Add an **External Connector** node (mode: NAT) and link it to SRV1's **second** interface (`ens3`).
2. On SRV1: `sudo dhclient ens3 && sudo apt update && sudo apt install -y dnsmasq nginx`
3. Delete the External Connector link. (Check your CML licensing page for whether External Connectors count toward the 20-node limit on your version.)

## 2. Static address — `/etc/netplan/50-cloud-init.yaml`

```yaml
network:
  version: 2
  ethernets:
    ens2:
      addresses: [10.10.50.10/24]
      routes:
        - to: default
          via: 10.10.50.1
      nameservers:
        addresses: [127.0.0.1]
        search: [ssbs.internal]
```

`sudo netplan apply`

## 3. DNS — `/etc/dnsmasq.d/ssbs.conf`

```ini
# Serve the internal zone; forward everything else to the "internet" (ISP1)
domain=ssbs.internal
local=/ssbs.internal/
server=8.8.8.8
bind-interfaces
listen-address=10.10.50.10,127.0.0.1
no-resolv

host-record=srv1.ssbs.internal,10.10.50.10
host-record=app.ssbs.internal,10.10.50.10
host-record=hqrt1.ssbs.internal,10.10.99.1
host-record=hqsw1.ssbs.internal,10.10.99.11
host-record=hqsw2.ssbs.internal,10.10.99.12
host-record=hqsw3.ssbs.internal,10.10.99.13
host-record=brrt1.ssbs.internal,10.20.99.1
host-record=brsw1.ssbs.internal,10.20.99.11
```

`bind-interfaces` + explicit `listen-address` avoids the port-53 clash with Ubuntu's systemd-resolved stub (127.0.0.53).

`sudo systemctl restart dnsmasq && sudo systemctl enable dnsmasq`

## 4. Web app

```bash
echo '<h1>SSBS Internal App</h1><p>Served from SRV1 10.10.50.10</p>' | sudo tee /var/www/html/index.html
sudo systemctl enable --now nginx
```

## Verify

```bash
dig @10.10.50.10 app.ssbs.internal +short     # 10.10.50.10
dig @10.10.50.10 www.example.com +short       # 8.8.8.8 (forwarded to ISP1)
curl -s http://app.ssbs.internal | head -1
```
