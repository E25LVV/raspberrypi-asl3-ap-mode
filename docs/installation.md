# Installation Guide

## Overview

This guide explains how to turn Raspberry Pi into a private Wi-Fi AP for portable AllStarLink 3 operation and mobile SSH administration.

คู่มือนี้อธิบายวิธีเปลี่ยน Raspberry Pi ให้เป็น Wi-Fi AP ส่วนตัว
สำหรับใช้งาน AllStarLink 3 แบบ portable
และบริหารระบบผ่าน SSH จากมือถือหรือโน้ตบุ๊ก

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

## Before You Start

ก่อนเริ่มติดตั้ง ควรตรวจสอบ:

- ระบบสามารถเชื่อมต่อ Internet ได้
- SSH ทำงานปกติ
- อัปเดตระบบเรียบร้อย
- สำรอง configuration สำคัญไว้ก่อน

---

## Package Installation

ขั้นตอนนี้จะติดตั้ง package ที่จำเป็นสำหรับ AP Mode

ตัวอย่าง package ที่ใช้งาน:

- hostapd
- dnsmasq

---

## Configure Wi-Fi AP

ขั้นตอนนี้จะกำหนดค่า Wi-Fi Access Point

ระบบจะสร้าง:
- SSID
- Password
- Wi-Fi Channel

สำหรับการเชื่อมต่อแบบ private management network

---

## Configure DHCP

ขั้นตอนนี้จะกำหนดระบบแจก IP address
ให้มือถือหรือโน้ตบุ๊กที่เชื่อมต่อเข้ามา

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
