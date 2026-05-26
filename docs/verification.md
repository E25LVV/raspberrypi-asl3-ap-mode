# Verification Guide

คู่มือตรวจสอบระบบหลังตั้งค่า Raspberry Pi AP Mode

ใช้สำหรับตรวจสอบว่า:
- Access Point ทำงานปกติ
- DHCP แจก IP ได้
- SSH ใช้งานได้
- AllStarLink service ยังทำงานปกติ

---

# Verify AP Interface

ตรวจสอบว่า wlan0 มี static IP หรือไม่

```bash
ip addr show wlan0
```

## Check AP IP Address

ควรมองเห็นลักษณะใกล้เคียงนี้:

```text
inet 192.168.8.1/24
```
ถ้ายังไม่พบ IP:

- restart network service
- ตรวจสอบ configuration อีกครั้ง
- ไปที่ [Troubleshooting Guide](troubleshooting.md)

---

## Ping Test

ทดลอง ping จากอุปกรณ์ที่เชื่อมต่อ AP:

```bash
ping 192.168.8.1
```
---
ถ้ามี reply กลับมา
แสดงว่า AP ยังตอบสนองปกติ

ตัวอย่าง:

```text
64 bytes from 192.168.8.1
```

---

## Check hostapd Status

ตรวจสอบว่า hostapd service ยังทำงานอยู่:

```bash
sudo systemctl status hostapd
```
ถ้าระบบปกติ
จะเห็น:

```text
active (running)
```

---
## Reality Notes

ถ้าเห็น Wi-Fi แต่เชื่อมต่อไม่ได้

สิ่งที่ควรตรวจสอบก่อน:


- password Wi-Fi
- restart hostapd
- reboot Raspberry Pi

บางครั้ง service อาจยังไม่โหลดค่าล่าสุด

---
Next:
- [Troubleshooting Guide](troubleshooting.md)
