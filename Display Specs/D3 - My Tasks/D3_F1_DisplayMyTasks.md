# D3 — F1: Display My Tasks

> **Display:** D3 | **Function:** 1 of 3
> **Back to overview:** [D3_Overview.md](D3_Overview.md)

---

**Objective:** แสดง FTT ที่ User จองไว้ในรูปแบบ 3-Panel Layout เพื่อให้ User ทำการรีวิวและตัดสินใจ

---

## Display Condition

> แสดงเฉพาะ FTT ที่:
> 1. อยู่ใน state ที่ตรงกับ Role ของ User (`PENDING_CHECKER` หรือ `PENDING_SIGNER`)
> 2. **ถูกจองโดย User คนนี้** (assigned_to = current_user)

---

## Panel 1 — FTT Card List (ซ้าย)

**Header:** รายการทั้งหมด (N รายการ)

### Card Fields

| Field | Description | Display Format |
|-------|-------------|----------------|
| เลขที่อ้างอิง | Reference number | Text |
| ประเภทรายการ | System หรือ Manual | Badge: System (ฟ้า `#4A90D9`) / Manual (ชมพู `#FFAFAF`) |
| ช่องทางคำขอ | Online หรือ Branch | Badge: **Online** (ม่วง) — แสดงเฉพาะเมื่อ channel = Online / Branch = ไม่แสดง Badge |
| จำนวนเงินสุทธิ | ยอดโอน | ตัวเลข (บาท) |
| ประเภทงาน | สินเชื่อ / คืนเงิน | Text |
| เวลางานเข้า | วันที่ / เวลาที่ FTT เข้าคิว | DD MMM YY \| HH:MM น. |
| ผู้ตรวจ | ชื่อ User ที่จองงาน (ตัวเอง) | Text |

### Badge Display Rules

| ช่องทางคำขอ | Badge แสดง |
|------------|-----------|
| Online | [System] [Online] หรือ [Manual] [Online] |
| Branch | [System] หรือ [Manual] เท่านั้น |

**Action Button:** ปุ่ม **คืนงาน** (แสดงทุก Card)

> **ไม่มี Filter** สำหรับ Panel 1 — User เห็นทุก FTT ที่ตัวเองจองไว้เท่านั้น

### Card States

| State | Visual |
|-------|--------|
| ถูกเลือก | กรอบสีฟ้า (Blue border highlight) |
| ไม่ถูกเลือก | ไม่มีกรอบพิเศษ |

### Empty State

| Condition | Display |
|-----------|---------|
| ไม่มี FTT ที่จองไว้ | แสดงข้อความ **"ไม่มีข้อมูล"** ใน Panel 1 |
| ยังไม่ได้เลือก Card | Panel 2 และ Panel 3 ว่างเปล่า |

> เมื่อ User กด Card ใดใน Panel 1 → Panel 2 และ Panel 3 จะโหลดข้อมูลของ FTT นั้น

---

## Panel 2 — Document Viewer (กลาง)

แสดงเอกสารประกอบของ FTT ที่เลือกใน Panel 1

### Document Tabs

| Tab | เอกสาร |
|-----|--------|
| 1 | สมุดบัญชี |
| 2 | บัตรประชาชน |
| 3 | ใบเปลี่ยนชื่อ-นามสกุล |
| 4 | ทะเบียนสมรส |
| 5 | แบบสัมภาษณ์ |
| 6 | รูปอื่นๆ |

> แสดง Tab ทั้งหมดเสมอ — หากไม่มีเอกสารในบาง Tab ให้แสดงสถานะ "ไม่มีเอกสาร" หรือ placeholder

---

## Panel 3 — FTT Detail (ขวา)

แสดงรายละเอียดของ FTT ที่เลือก ประกอบด้วย 3 ส่วนหลัก:

### ส่วนที่ 1 — ข้อมูลรายการ

| Field | Description |
|-------|-------------|
| เลขที่อ้างอิง | Reference number + Badge (System/Manual) + Badge Online (ถ้า channel = Online) |
| รหัสรายการ | Internal transaction ID |
| ประเภทงาน | สินเชื่อ / คืนเงิน |
| เลขบัตรประชาชน | เลขบัตรของผู้รับโอน |
| ประเภทสินเชื่อ | ประเภทสินเชื่อที่เกี่ยวข้อง |
| หลักประกัน | ข้อมูลหลักประกัน |
| ชื่อ-สกุล (ภาษาไทย) | ชื่อผู้รับโอน + ตัวบ่งชี้ความตรง |
| ชื่อ-สกุล (ภาษาอังกฤษ) | ชื่อผู้รับโอน (EN) + ตัวบ่งชี้ความตรง |
| ช่องทาง | Online / Branch |
| เลขที่บัญชี / พร้อมเพย์ | บัญชีปลายทาง |
| จำนวนเงินสุทธิ | ยอดโอน (บาท) |

### ตัวบ่งชี้ความตรงของชื่อ-สกุล

| ผล | Display |
|----|---------|
| ชื่อตรงกับข้อมูลธนาคาร | **ตรง** (สีเขียว) |
| ชื่อไม่ตรงกับข้อมูลธนาคาร | **ไม่ตรง** (สีแดง) |

### ส่วนที่ 2 — การอนุมัติ (Action Section)

> **ไม่แสดง**ประวัติ Checker / Signer ก่อนหน้าใน Panel นี้
> User สามารถดู Action และ State History ได้โดยกด Link **ประวัติการทำรายการ** → เข้าสู่ Status Page (D4/D5)

| Element | Description |
|---------|-------------|
| Header | **การอนุมัติ** |
| ประวัติการทำรายการ | Link (มุมขวาบน) → เปิด Status Page |
| ปุ่ม ยกเลิก | สีแดง — เปิด Rejection Form ใน Panel 3 |
| ปุ่ม ยืนยัน | สีเขียว — เปิด Confirmation Modal |
| หมายเหตุ | Text field (Optional) — Placeholder: *"กรุณาระบุข้อความหรือชื่อไฟล์"* |
