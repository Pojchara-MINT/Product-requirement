# D2 — F1: Display All Tasks

> **Display:** D2 | **Function:** 1 of 3
> **Back to overview:** [D2_Overview.md](D2_Overview.md)

---

**Objective:** แสดง FTT ทั้งหมดในคิวของ Role ปัจจุบัน ในรูปแบบตาราง

---

## Display Condition

> แสดงเฉพาะ FTT ที่:
> 1. อยู่ใน state ที่ตรงกับ Role ของ User ที่ Login อยู่ (`PENDING_CHECKER` หรือ `PENDING_SIGNER`)
> 2. **ยังไม่ถูกจองโดย User คนนี้** — FTT ที่ User จองแล้วจะไม่ปรากฏใน List นี้

---

## Table Display

แสดงทุก FTT ในคิวเรียงตาม เวลางานเข้า (เก่าไปใหม่)

| Column | Description | Display Format |
|--------|-------------|----------------|
| เลขที่อ้างอิง | Reference number | 🔵 dot = System / 🔴 dot = Manual |
| ประเภท | ประเภทของรายการ | Text |
| ช่องทาง | Online / Branch | Text |
| ประเภทงาน | สินเชื่อ / คืนเงิน | Text |
| ชื่อ-สกุล | ชื่อผู้รับโอน | Text |
| เวลางานเข้า | วันที่ / เวลาที่ FTT เข้าคิว | DD ม.ค. YY \| HH:MM น. |
| ผลตรวจสอบบัญชีปลายทาง | ผล Bank Step 1 (Verify Account) | Badge: ผ่าน (เขียว) / ไม่ผ่าน (แดง) / ยังไม่ได้รับผล (เทา) |
| ผลเงื่อนไขโอนเงิน | ผล Internal Rule Check | Badge: ผ่าน (เขียว) / ไม่ผ่าน (แดง) / ยังไม่ได้รับผล (เทา) |
| สถานะงาน | พร้อมตรวจ / ไม่พร้อมตรวจ | Badge: พร้อมตรวจ (เขียว) / ไม่พร้อมตรวจ (ส้ม) |
| ผู้ตรวจ | ชื่อ User ที่จองงานอยู่ | Text หรือ `-` ถ้าไม่มีผู้จอง |
| ทำรายการ | — | ปุ่ม **จองงาน** |

**Footer:** รายการทั้งหมด: N รายการ
