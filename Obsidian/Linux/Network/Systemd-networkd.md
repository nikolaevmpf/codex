systemctl enable systemd-networkd.service 
systemctl enable systemd-resolved.service 

## Static
nano /etc/systemd/network/20-wired.network

	[Match]
	Name = enp2s1
	[Network]
	Address = 10.2.1.111/24
	Gateway = 10.2.1.1
	LinkLocalAddressing = no  # отключить ipv6
	IPv6AcceptRA = no  # отключить ipv6

nano /etc/systemd/resolved.conf

	[resolve]
	DNS = 10.2.1.11
	DNS = 10.2.1.218
	Domains = gz.local

## DHCP
nano /etc/systemd/network/20-wired.network

	[Match]
	Name = enp2s1
	[Network]
	DHCP = yes
---
