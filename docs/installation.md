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

ขั้นตอนนี้จะเปิดให้บริหาร Raspberry Pi ผ่าน SSH
จากมือถือหรือโน้ตบุ๊กผ่าน Wi-Fi AP

---

## Enable Services

ขั้นตอนนี้จะเปิดใช้งาน service ที่จำเป็น
ให้เริ่มทำงานอัตโนมัติหลัง reboot

---

## Reboot System

หลังติดตั้งเสร็จ
ควร reboot ระบบเพื่อทดสอบการทำงานจริง

---

## Verify AP Mode

หลัง reboot ควรตรวจสอบ:

- Raspberry Pi ปล่อย Wi-Fi ได้
- มือถือเชื่อมต่อได้
- SSH เข้าได้
- AllStarLink ยังทำงานปกติ

---

## Troubleshooting

หากระบบทำงานผิดปกติ:

- ตรวจสอบ hostapd status
- ตรวจสอบ dnsmasq status
- ตรวจสอบ IP address
- ตรวจสอบ Wi-Fi interface

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
