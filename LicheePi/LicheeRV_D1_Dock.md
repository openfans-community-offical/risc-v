## WIFI Connect

**Image:** 20211230_LicheeRV_debian_d1_hdmi_8723ds.img

Edit file: **/etc/wifi/wpa_supplicant.conf**

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

Add file: **/usr/sbin/wifi_connect.sh**

```
#!/bin/bash
## custom network conf
ifconfig wlan0 up &
wpa_supplicant -B -D nl80211 -i wlan0 -c /etc/wifi/wpa_supplicant.conf &
sleep 5
dhclient &
exit 0
```

Add file : **/lib/systemd/system/wifi-connect.service**

``` 
[Unit]
Description=wifi connect
After=ssh.service

[Service]
Type=notify
ExecStart=/usr/sbin/wifi_connect.sh
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=30s

[Install]
WantedBy=multi-user.target
```

Enable Wifi Connect AutoStart

```
systemctl enable wifi-connect.service
systemctl is-enabled wifi-connect.service
```
