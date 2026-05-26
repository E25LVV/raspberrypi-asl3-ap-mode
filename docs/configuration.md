# Configuration Guide

คู่มือ configuration สำหรับ Raspberry Pi AP Mode บน AllStarLink 3

---

# Configure hostapd

แก้ไขไฟล์ configuration:

```bash
sudo nano /etc/hostapd/hostapd.conf
```
ตัวอย่าง configuration:

```ini
interface=wlan0
ssid=ASL3-AP
hw_mode=g
channel=6
wmm_enabled=0
auth_algs=1
ignore_broadcast_ssid=0
