---
layout: post
title: systemd-logind, Unit suspend.target is masked, refusing operation.
description: >
  systemd-logind, auth.log
sitemap: false
hide_last_modified: true
categories: [linux, server, network, nmcli, NetworkManager, wireless, wired]
tags:       [linux, server, network, nmcli, NetworkManager, wireless, wired]
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- ubuntu 22.04 live server amd64

## Reference site

- [CentOS nmcli 명령어로 정적 ip (Static IP) 설정하기] &rarr; lesstif.com
- ["nmcli"를 이용하여 Wi-Fi AP 연결하기] &rarr; linkedin.com

## install network-manager

```sh
# install network-manager
sudo apt-get install network-manager -yq
```

{:.related-posts.faded}

## wireless

### activate Wi-Fi

```sh
# verify wifi enabled
nmcli radio wifi

# turn on wifi enabled
nmcli radio wifi on

# verify wifi enabled
nmcli radio wifi
```

{:.related-posts.faded}

### inquiry available list of Wi-Fi AP

```sh
nmcli device wifi list

# check following result
IN-USE  BSSID              SSID             MODE   CHAN  RATE        SIGNAL  BARS  SECURITY

...skipping...

        D6:9E:43:9F:E3:A4  available_WiFi_a	Infra  1     130 Mbit/s  92      ▂▄▆█  WPA2

...skipping...

```

{:.related-posts.faded}

### connect Wi-Fi AP

```sh
nmcli device wifi connect available_WiFi_a password <PASSWORD>
```

{:.related-posts.faded}

### Verify Wi-Fi AP Connectivity

```sh

nmcli device wifi list

# check following result
IN-USE  BSSID              SSID             MODE   CHAN  RATE        SIGNAL  BARS  SECURITY

*       D2:9E:43:9F:E3:A6  available_WiFi_a	Infra  1     130 Mbit/s  92      ▂▄▆█  WPA2

```

{:.related-posts.faded}

### View Wi-Fi AP Connectivity Details

```sh

nmcli -f all device wifi list

# check following result
NAME    SSID             SSID-HEX                        BSSID              MODE   CHAN  FREQ      RATE        SIGNAL

AP[10]  available_WiFi_a 4561737943657274695F65          8A:CC:9C:99:73:4E  Infra  157   5785 MHz  270 Mbit/s  80

```

{:.related-posts.faded}

### set static ip using nmcli ( NetworkManager )

```sh

# set static ip
sudo nmcli con mod EasyCerti_e \
		ipv4.address 192.168.15.250/24 \
		ipv4.gateway 192.168.15.1 \
		ipv4.dns 192.168.15.1 \
		ipv4.method manual

# apply connection
sudo nmcli con up EasyCerti_e
```

{:.related-posts.faded}

## wired

### add device and static ip using nmcli

- connection __static_ether__ as `static ip`

```sh

# connection `static_ether` as static ip
sudo nmcli con add \
		con-name static_ether ifname enp6s0 \
		autoconnect no \
		type ethernet \
		ip4 192.168.15.250/24 \
		gw4 192.168.1.1 \
		ipv4.method manual

# connection up
sudo nmcli con up static_ether

# view connection
sudo nmcli con show

# view ip address
ip a show enp6s0

# view ip route
ip route | grep default

```

- connection __dhcp_ether__ as `dhcp ip`

```sh

# connection `dhcp_ether` as dhcp ip
sudo nmcli con add \
		con-name dhcp_ether ifname enp6s0 \
		autoconnect no \
		type ethernet \
		ipv4.method auto

# connection up
sudo nmcli con up dhcp_ether

# view connection
sudo nmcli con show

# view ip address
ip a show enp6s0

# view ip route
ip route | grep default

```

- modify

```sh

# connection not allow auto connect
nmcli con mod <connection_name> connection.autoconnect no

# use ip4 instead of ipv4.
# to add a second item, add a `+`
nmcli con mod <connection_name> ipv4.dns 192.168.15.1 

nmcli con mod <connection_name> +ipv4.dns 8.8.8.8

# to add the second item, add `+`
nmcli con mod <connection_name> ipv4.addresses 192.168.15.250/24

nmcli con mod <connection_name> +ipv4.addresses 192.168.1.251/24

# to apply connection configure
nmcli con up <static/dhcp>
```

{:.related-posts.faded}

[CentOS nmcli 명령어로 정적 ip (Static IP) 설정하기]:https://www.lesstif.com/system-admin/centos-nmcli-ip-static-ip-98926807.html
["nmcli"를 이용하여 Wi-Fi AP 연결하기]:https://kr.linkedin.com/pulse/nmcli%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-wi-fi-ap-%EC%97%B0%EA%B2%B0%ED%95%98%EA%B8%B0-jun-hee-shin