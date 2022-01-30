## WIFI Connect

**Image:** 20211230_LicheeRV_debian_d1_hdmi_8723ds.img

- **user:** sipeed

- **password:** licheepi

----

#### 1. Enable Wifi

- (1) Click "**Start**" -> "**Preferences**" -> "**Connman Settings**" for open window:

- (2) "**Enable**" WiFi for "**OPEN**"

#### 2. Scripts and conf

- 2.1 Edit file: **/etc/wifi/wpa_supplicant.conf**

```
ctrl_interface=/etc/wifi/sockets
country=CN
disable_scan_offload=1
update_config=1
wowlan_triggers=any
network={
ssid="XXXXXX"
psk="0123456789"
}
```

- 2.2 Add file: **/usr/sbin/wifi_connect.sh**

```
#!/bin/bash
## custom network conf
rm -rf /etc/wifi/sockets/wlan0 2>/dev/null
ifconfig wlan0 down 2>/dev/null
ifconfig wlan0 up &
wpa_supplicant -B -D nl80211 -i wlan0 -c /etc/wifi/wpa_supplicant.conf &
sleep 5
dhclient &
exit 0
```

then

```
chmod +x /usr/sbin/wifi_connect.sh
```

- 2.3 Add file : **/lib/systemd/system/wifi-connect.service**

``` 
[Unit]
Description=wifi connect
Requires=network.target
After=network.target

[Service]
Type=forking
ExecStart=/usr/sbin/wifi_connect.sh

[Install]
WantedBy=multi-user.target
```

- 2.4 Enable Wifi Connect AutoStart

```
systemctl enable wifi-connect.service
systemctl is-enabled wifi-connect.service
```

## PS:

#### aliyun risc-v repo:

```
deb https://mirrors.aliyun.com/debian-ports/ sid main
deb https://mirrors.aliyun.com/debian-ports/ unreleased main
```

#### change run level

**graphical.target**

```
systemctl set-default graphical.target
```

**multi-user.target**

```
systemctl set-default multi-user.target
```

**immediately**

```
systemctl isolate multi-user.target
systemctl isolate graphical.target
```
