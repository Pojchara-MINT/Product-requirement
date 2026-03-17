# D2 — All Tasks / งานทั้งหมด (Overview)

> **Feature:** F4 — FTT Main Structure + D2 + Verification + Distribution
> **Version:** 0.1 Draft
> **Last Revised:** 2026-03-11
> **Primary Actor:** Checker, Signer

---

## Page Layout

Table format — แสดง FTT ทั้งหมดในคิวของ Role ปัจจุบัน

**Top Navigation Tabs:** สร้างรายการใหม่ | งานของฉัน | **งานทั้งหมด** | สถานะงาน

**Header:** กรองข้อมูล | Legend: 🔵 System / 🔴 Manual

**Footer:** รายการทั้งหมด: N รายการ

---

## Display Condition (Role-Based)

| Role | เห็น FTT สถานะ | ไม่เห็น |
|------|--------------|---------|
| Checker | `PENDING_CHECKER` เท่านั้น | PENDING_SIGNER และสถานะอื่น |
| Signer | `PENDING_SIGNER` เท่านั้น | PENDING_CHECKER และสถานะอื่น |

> Checker และ Signer **ไม่สามารถเห็นคิวของกันและกัน** ได้เด็ดขาด

---

## Table Columns

| Column | Description | Display Format |
|--------|-------------|----------------|
| เลขที่อ้างอิง | Reference number + dot badge | 🔵 System / 🔴 Manual |
| ประเภท | ประเภทของรายการ (e.g., Top up กู้เงินเพิ่ม) | Text |
| ช่องทาง | Online / Branch | Text |
| ประเภทงาน | สินเชื่อ / คืนเงิน | Text |
| ชื่อ-สกุล | ชื่อผู้รับโอน | Text |
| เวลางานเข้า | วันที่ / เวลาที่ FTT เข้าคิว | DD ม.ค. YY \| HH:MM น. |
| ผลตรวจสอบบัญชีปลายทาง | ผลการ Verify Account (Bank Step 1) | Badge: ผ่าน (เขียว) / ไม่ผ่าน (แดง) / ยังไม่ได้รับผล (เทา) |
| ผลเงื่อนไขโอนเงิน | ผลการตรวจสอบ Internal Rule | Badge: ผ่าน (เขียว) / ไม่ผ่าน (แดง) / ยังไม่ได้รับผล (เทา) |
| สถานะงาน | ความพร้อมในการตรวจ | Badge: พร้อมตรวจ (เขียว) / ไม่พร้อมตรวจ (ส้ม) |
| ผู้ตรวจ | ชื่อ User ที่จองงานไว้ | Text หรือ `-` ถ้ายังไม่มีผู้จอง |
| ทำรายการ | Action button | ปุ่ม **จองงาน** |

---

## สถานะงาน Logic

| สถานะงาน | เงื่อนไข |
|---------|---------|
| พร้อมตรวจ | ได้รับผลทั้งคู่แล้ว — ไม่ว่าจะผ่านหรือไม่ผ่าน (ผลตรวจสอบบัญชีปลายทาง ≠ ยังไม่ได้รับผล **และ** ผลเงื่อนไขโอนเงิน ≠ ยังไม่ได้รับผล) |
| ไม่พร้อมตรวจ | ยังไม่ได้รับผลอย่างน้อย 1 รายการ (ผลตรวจสอบบัญชีปลายทาง = ยังไม่ได้รับผล **หรือ** ผลเงื่อนไขโอนเงิน = ยังไม่ได้รับผล) |

---

## Main Functions

| # | Function | File |
|---|----------|------|
| 1 | Display All Tasks | [D2_F1_DisplayAllTasks.md](D2_F1_DisplayAllTasks.md) |
| 2 | Filter | [D2_F2_Filter.md](D2_F2_Filter.md) |
| 3 | จองงาน (Book Task) | [D2_F3_BookTask.md](D2_F3_BookTask.md) |

---

## State Transitions (D2 scope)

| Action | State Before | State After | Side Effect |
|--------|-------------|------------|-------------|
| จองงาน (Checker) | `PENDING_CHECKER` | `PENDING_CHECKER` (assigned) | FTT หายออกจาก Pool → ย้ายไป D3 งานของฉัน |
| จองงาน (Signer) | `PENDING_SIGNER` | `PENDING_SIGNER` (assigned) | FTT หายออกจาก Pool → ย้ายไป D3 งานของฉัน |
