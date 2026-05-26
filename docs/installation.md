# Installation Guide

## Overview

This guide explains how to turn Raspberry Pi into a private Wi-Fi AP for portable AllStarLink 3 operation and mobile SSH administration.

คู่มือนี้อธิบายวิธีเปลี่ยน Raspberry Pi ให้เป็น Wi-Fi AP ส่วนตัว
สำหรับใช้งาน AllStarLink 3 แบบ portable
และบริหารระบบผ่าน SSH จากมือถือหรือโน้ตบุ๊ก
---

## Before You Start

ก่อนเริ่มติดตั้ง AP Mode ควรตรวจสอบและเตรียมระบบให้พร้อมก่อนทุกครั้ง

การตั้งค่า AP Mode จะเปลี่ยนพฤติกรรม network ของ Raspberry Pi  
หากตั้งค่าผิด อาจทำให้:

- SSH เข้าไม่ได้
- Wi-Fi ใช้งานไม่ได้
- network configuration ผิดพลาด
- ต้องแก้ระบบผ่าน local console

แนะนำให้เตรียม:

- HDMI และ Keyboard สำหรับ recovery
- สำรองไฟล์ configuration สำคัญ
- ใช้ Power Supply ที่เสถียร
- อัปเดตระบบให้เรียบร้อยก่อนเริ่ม

สำหรับ AllStarLink 3 production node  
แนะนำให้ทดสอบบนระบบสำรองก่อนใช้งานจริงเสมอ

---

## Supported Systems

รองรับระบบ:

- Debian 12
- Debian 13
- AllStarLink 3

---

## Recommended Hardware

อุปกรณ์แนะนำ:

- Raspberry Pi 0 2 W / 3 / 4 / 5
- MicroSD Card คุณภาพดี
- Power Supply เสถียร
- Wi-Fi Interface ที่รองรับ AP Mode

---

## Network Concept

แนวคิดของระบบนี้คือ:

Raspberry Pi จะปล่อยสัญญาณ Wi-Fi ของตัวเอง
เพื่อให้มือถือหรือโน้ตบุ๊กเชื่อมต่อเข้ามาได้โดยตรง

เหมาะสำหรับ:
- Portable Node
- งานภาคสนาม
- Emergency Communication
- Temporary RF Site

---
### Example Network Topology

ตัวอย่าง topology ของระบบ:

Internet
    │
    │ (optional)
    │
Raspberry Pi (AllStarLink 3)
├── Wi-Fi AP Mode
├── hostapd
├── dnsmasq
└── SSH Server
    │
    ├── Mobile Phone
    ├── Laptop
    └── Tablet

---

แนวคิดของระบบนี้คือ:

Raspberry Pi จะสร้าง private management network ของตัวเอง
เพื่อให้สามารถบริหาร node ได้โดยตรงผ่าน Wi-Fi

ข้อดีของแนวทางนี้:
- ไม่ต้องพึ่ง router ภายนอก
- เหมาะกับงาน portable
- เหมาะกับงานภาคสนาม
- ลดปัญหา IP เปลี่ยน
- เชื่อมต่อ SSH ได้ง่ายจากมือถือ

---

ระบบนี้ไม่ได้ออกแบบมาเพื่อ:
- ปล่อย Internet hotspot ขนาดใหญ่
- รองรับ client จำนวนมาก
- ใช้งานแทน enterprise Wi-Fi system

เป้าหมายหลักคือ:
"ระบบบริหาร RF Node ผ่าน Wi-Fi ส่วนตัว"

## Before You Start

ก่อนเริ่มติดตั้ง AP Mode
ควรตรวจสอบและเตรียมระบบให้พร้อมก่อนทุกครั้ง

การข้ามขั้นตอนเหล่านี้
อาจทำให้:
- SSH เข้าไม่ได้
- Wi-Fi ใช้งานไม่ได้
- network configuration ผิดพลาด
- ต้องแก้ระบบผ่าน local console

---

### Recommended Preparation

แนะนำให้เตรียม:

- Keyboard และ HDMI สำหรับ recovery กรณี network มีปัญหา
- สำรองไฟล์ configuration สำคัญ
- ใช้ Power Supply ที่เสถียร
- อัปเดตระบบให้เรียบร้อยก่อนเริ่ม

---

### Verify Wi-Fi Interface

ตรวจสอบชื่อ Wi-Fi interface ก่อนทุกครั้ง

ตัวอย่าง interface ที่พบบ่อย:

- wlan0
- wlan1

สามารถตรวจสอบได้ด้วยคำสั่ง:

```bash
ip addr
```
---

## Verify Wi-Fi Interface

ก่อนเริ่มติดตั้ง ควรตรวจสอบชื่อ Wi-Fi interface ของระบบก่อนทุกครั้ง

Raspberry Pi แต่ละรุ่นอาจใช้ชื่อ interface แตกต่างกัน เช่น:

- wlan0
- wlan1

บางระบบ:
- wlan0 = internal Wi-Fi
- wlan1 = USB Wi-Fi adapter

สามารถตรวจสอบได้ด้วยคำสั่ง:

```bash
ip addr
```

ตรวจสอบให้แน่ใจว่า interface ที่จะใช้เป็น AP Mode ถูกต้องก่อนดำเนินการต่อ

สำหรับ AllStarLink 3 production node  
แนะนำให้แยก:
- interface สำหรับ Internet uplink
- interface สำหรับ AP Mode

ออกจากกันเสมอ เพื่อป้องกัน network conflict

---

## Package Installation

ขั้นตอนนี้จะติดตั้ง package ที่จำเป็นสำหรับ AP Mode


ก่อนติดตั้ง package แนะนำให้อัปเดตระบบก่อนทุกครั้ง

สำหรับ AllStarLink 3 production node:
ควรตรวจสอบว่าระบบ stable และไม่มี net กำลังใช้งานอยู่ก่อน update package

ตรวจสอบ package update:

```bash
sudo apt update
```

อัปเกรด package:

```bash
sudo apt upgrade -y
```

หาก kernel หรือ network package ถูกอัปเดต
แนะนำให้ reboot หลังอัปเกรดเสร็จ

ติดตั้ง package สำหรับ AP Mode:

```bash
sudo apt install hostapd dnsmasq -y
```

หน้าที่ของ package:

- hostapd = สร้าง Wi-Fi Access Point
- dnsmasq = แจก DHCP และจัดการ local DNS

ตรวจสอบว่าติดตั้งสำเร็จ:

```bash
systemctl status hostapd
```

```bash
systemctl status dnsmasq
```

หาก service ยังไม่ start ถือเป็นเรื่องปกติ
เพราะยังไม่ได้ configure AP Mode ในขั้นตอนถัดไป

---

## Configure Wi-Fi AP

ขั้นตอนนี้จะกำหนดค่า Wi-Fi Access Point สำหรับบริหาร AllStarLink node

Wi-Fi AP นี้ออกแบบสำหรับ:

- SSH บริหาร node
- maintenance
- portable operation
- emergency recovery

ไม่แนะนำให้ใช้เป็น public hotspot หรือแชร์ Internet สำหรับผู้ใช้จำนวนมาก

สำหรับ AllStarLink 3 production node
ควรแยกหน้าที่ของ network interface ให้ชัดเจน:

- interface สำหรับ Internet uplink
- interface สำหรับ AP Mode

เพื่อลดปัญหา network conflict และป้องกัน node หลุดจากระบบ

แนะนำการตั้งชื่อ SSID:

- ASL-Portable
- ASL-Mobile
- E25LVV-Node

ควรตั้งชื่อที่ช่วยให้รู้ว่าเป็น node อะไร
โดยเฉพาะเวลาทดสอบหลายระบบพร้อมกัน

แนะนำการตั้งรหัสผ่าน:

- ใช้รหัสผ่านยาวอย่างน้อย 8 ตัวอักษร
- หลีกเลี่ยงรหัสผ่านสั้นหรือเดาง่าย
- ไม่ควรใช้รหัสเดียวกับ Wi-Fi บ้าน

เรื่อง Wi-Fi Channel:

หากใช้งานในพื้นที่ที่มี Wi-Fi หนาแน่น
อาจต้องเปลี่ยน channel ภายหลัง
เพื่อลดสัญญาณรบกวนและเพิ่มเสถียรภาพของ AP

สำหรับ portable node ภาคสนาม
ควรทดสอบระยะสัญญาณจริงก่อนใช้งานทุกครั้ง

ตัวอย่าง package ที่เกี่ยวข้องกับ AP Mode:

- hostapd = สร้าง Wi-Fi AP
- dnsmasq = แจก DHCP และจัดการ local DNS


---

## Configure DHCP

ขั้นตอนนี้จะกำหนด management network สำหรับ AP Mode

เมื่อมือถือหรือโน้ตบุ๊กเชื่อมต่อเข้ากับ Wi-Fi AP
ระบบจะได้รับ IP address อัตโนมัติผ่าน DHCP

สำหรับ portable AllStarLink node
แนะนำให้ใช้ private subnet แยกเฉพาะสำหรับ management network

ตัวอย่าง subnet ที่นิยมใช้:

```bash
192.168.50.0/24
```

ตัวอย่าง:

- Raspberry Pi node = 192.168.50.1
- มือถือหรือโน้ตบุ๊ก = รับ IP อัตโนมัติจาก DHCP

ข้อดีของการแยก subnet:

- ลดปัญหา network conflict
- SSH เข้า node ได้ง่าย
- แยกจาก Wi-Fi บ้านหรือ hotspot
- เหมาะกับ portable operation

หาก subnet ชนกับ network อื่น
อาจเกิดอาการ:

- มือถือเชื่อม Wi-Fi ได้ แต่ SSH ไม่เข้า
- เข้า Allmon ไม่ได้
- route ผิดพลาด
- node หลุดจาก management network

สำหรับ mobile SSH operation
แนะนำให้จด IP address ของ node ไว้เสมอ

ตัวอย่าง:

```bash
192.168.50.1
```

ในกรณี DHCP มีปัญหา
ยังสามารถ SSH เข้า node แบบ manual ได้

สำหรับ production node ภาคสนาม
ควรหลีกเลี่ยงการเปลี่ยน subnet บ่อย
เพื่อลดปัญหาการจำค่า network ของอุปกรณ์

---

## Configure SSH Access

ขั้นตอนนี้จะเปิดให้บริหาร Raspberry Pi ผ่าน SSH ผ่าน Wi-Fi AP

SSH ช่วยให้สามารถ:

- บริหาร node จากมือถือ
- remote maintenance
- ตรวจสอบ log
- restart service
- recovery ระบบเบื้องต้น

สำหรับ portable AllStarLink node
SSH ถือเป็นเครื่องมือหลักในการบริหารระบบ

แนะนำ application สำหรับ mobile SSH:

- Termius
- JuiceSSH
- PuTTY
- OpenSSH

ตัวอย่างการเชื่อมต่อ:

```bash
ssh pi@192.168.50.1
```

ตัวอย่างนี้:

- pi = username ของ Raspberry Pi
- 192.168.50.1 = IP address ของ node

สำหรับ production node
แนะนำให้กำหนด IP ของ node ให้คงที่เสมอ
เพื่อให้ SSH ได้ง่ายและลดปัญหาการจำค่า network

หาก SSH เข้าไม่ได้ ให้ตรวจสอบ:

- มือถือเชื่อมต่อ AP ถูกตัวหรือไม่
- subnet ชนกับ network อื่นหรือไม่
- firewall หรือ service SSH ทำงานอยู่หรือไม่
- Raspberry Pi ยัง online อยู่หรือไม่

ในกรณี emergency recovery:

- ใช้ HDMI + Keyboard
- ใช้ local console
- ตรวจสอบ IP address ใหม่
- reboot node หากจำเป็น

สำหรับ node ภาคสนาม
ควรทดสอบ SSH ผ่านมือถือจริงก่อนใช้งานทุกครั้ง


---

## Enable Services

ขั้นตอนนี้จะเปิดใช้งาน service ให้เริ่มทำงานอัตโนมัติหลัง reboot

สำหรับ portable AllStarLink node ถือเป็นขั้นตอนสำคัญ
เพราะหากไม่ enable service ระบบ AP อาจไม่ทำงานหลังเปิดเครื่องใหม่

เปิดใช้งาน service:

```bash
sudo systemctl enable hostapd
```

```bash
sudo systemctl enable dnsmasq
```

เริ่ม service ทันทีโดยไม่ต้อง reboot:

```bash
sudo systemctl start hostapd
```

```bash
sudo systemctl start dnsmasq
```

ตรวจสอบสถานะ service:

```bash
systemctl status hostapd
```

```bash
systemctl status dnsmasq
```

หาก service ยังไม่ start:
- ตรวจสอบ configuration file
- ตรวจสอบ interface name
- ตรวจสอบว่า port หรือ service ไม่ชนกัน

สำหรับ production node:
แนะนำให้ reboot และทดสอบ AP จริงทุกครั้งหลัง configuration เสร็จ

---

## Reboot System

หลังติดตั้งและ configure เสร็จ
ควร reboot ระบบเพื่อทดสอบการทำงานจริงของ AP Mode

reboot ระบบ:

```bash
sudo reboot
```

หลัง reboot:
รอประมาณ 1-2 นาที ให้ Raspberry Pi boot เสร็จสมบูรณ์

จากนั้นตรวจสอบ:

- Wi-Fi AP ปรากฏหรือไม่
- มือถือหรือโน้ตบุ๊กเชื่อมต่อได้หรือไม่
- ได้รับ IP address จาก DHCP หรือไม่
- SSH เข้า node ได้หรือไม่
- AllStarLink service ยังทำงานปกติหรือไม่

สำหรับ production node:
การ reboot test ถือเป็นขั้นตอนสำคัญ
เพราะบางระบบสามารถ start service ได้ตอน manual start
แต่ล้มเหลวหลัง reboot จริง

หาก reboot แล้ว AP ไม่ขึ้น:
- ตรวจสอบ hostapd status
- ตรวจสอบ dnsmasq status
- ตรวจสอบ interface name
- ตรวจสอบ power supply ของ Raspberry Pi

ตัวอย่างตรวจสอบ service:

```bash
systemctl status hostapd
```

```bash
systemctl status dnsmasq
```

สำหรับ portable node ภาคสนาม:
แนะนำให้ทดสอบ reboot จริงหลายรอบ
ก่อนนำไปใช้งานนอกสถานี

---

## Verify AP Mode

หลัง reboot และเชื่อมต่อ Wi-Fi AP สำเร็จ
ควรตรวจสอบระบบจริงก่อนนำไปใช้งานภาคสนาม

ตรวจสอบพื้นฐาน:

- Raspberry Pi ปล่อย Wi-Fi AP ได้
- มือถือหรือโน้ตบุ๊กเชื่อมต่อได้
- ได้รับ IP address จาก DHCP
- SSH เข้า node ได้
- AllStarLink service ยังทำงานปกติ

ตรวจสอบ IP address ของอุปกรณ์:

```bash
ip addr
```

ตรวจสอบว่า interface AP มี IP address ถูกต้อง

ตัวอย่าง:

```text
192.168.50.1
```

ทดสอบ SSH จากมือถือหรือโน้ตบุ๊ก:

```bash
ssh pi@192.168.50.1
```

หาก SSH เข้าได้:
ถือว่า management network พร้อมใช้งาน

สำหรับ AllStarLink node:
ควรตรวจสอบเพิ่มเติมว่า

- app_rpt ยังทำงานปกติ
- node ยัง online
- link ยังเชื่อมต่อได้
- Allmon หรือ Supermon ยังเข้าถึงได้

สำหรับ portable node ภาคสนาม:
แนะนำให้ทดสอบจริงดังนี้

- reboot หลายรอบ
- เชื่อมต่อมือถือหลายเครื่อง
- ทดสอบ SSH ระยะไกล
- ทดสอบขณะใช้งาน RF จริง
- ตรวจสอบว่า Wi-Fi ไม่หลุดระหว่างใช้งาน

หากทุกอย่างทำงานปกติ:
ถือว่า AP Mode พร้อมใช้งานจริง

---

## Troubleshooting

หากระบบทำงานผิดปกติ
แนะนำให้ตรวจสอบตามอาการที่พบจริง

---

### AP ไม่ปรากฏ

ตรวจสอบ:

- hostapd ทำงานหรือไม่
- Wi-Fi interface ถูกต้องหรือไม่
- Raspberry Pi รองรับ AP Mode หรือไม่
- power supply เสถียรหรือไม่

ตรวจสอบ service:

```bash
systemctl status hostapd
```

ตรวจสอบ interface:

```bash
ip addr
```

บางกรณี interface อาจไม่ใช่ wlan0

---



## Rollback Procedure

หาก AP Mode มีปัญหา
สามารถย้อนกลับ configuration เดิมได้

ควรสำรองไฟล์ configuration ก่อนแก้ไขทุกครั้ง

---

## Recovery Procedure

หากระบบเชื่อมต่อไม่ได้หลังแก้ไข configuration:

- ใช้ local console
- ใช้ HDMI + Keyboard
- คืนค่า configuration เดิม
- reboot ระบบใหม่

---
## Next Step

หลังติดตั้งเสร็จ:

- ไปต่อที่ configuration.md
- ตรวจสอบระบบที่ verification.md
- หากพบปัญหา ดู troubleshooting.md
