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

# Troubleshooting

หากพบปัญหา:

- ดู troubleshooting.md
- หรือ rollback ที่ rollback.md
