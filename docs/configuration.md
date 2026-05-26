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
```

## Known Working Scope

ทดสอบแล้วบน:

- Raspberry Pi 4
- Debian 12
- ASL3
- onboard Wi-Fi

ยังไม่ได้ทดสอบ:

- USB Wi-Fi dongle
- dual-band tuning
- advanced roaming setup

---

Next:
- [Verification Guide](verification.md)
