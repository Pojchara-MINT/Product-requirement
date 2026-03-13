# D4 — Transaction History & Detail / สถานะงาน (Overview)

> **Feature:** F6 — Transaction History & Detail
> **Version:** 0.1 Draft
> **Last Revised:** 2026-03-12
> **Primary Actor:** All roles (Maker, Checker, Signer, Ops Manager)

---

## Page Access

User เข้าถึง D4 ได้จาก 2 ทาง:

| Entry Point | Location | Action |
|-------------|----------|--------|
| Top Navigation Tab | **สถานะงาน** | คลิก Tab จากหน้าใดก็ได้ |
| ประวัติการทำรายการ | Link ใน D3 Panel 3 (การอนุมัติ section) | คลิก Link จาก FTT ที่กำลังรีวิว |

---

## Page Layout

**Top Navigation Tabs:** สร้างรายการใหม่ | งานของฉัน | งานทั้งหมด | **สถานะงาน**

D4 มี 2 ระดับ:

| ระดับ | ชื่อ | หน้าที่ |
|-------|------|---------|
| L1 | Transaction History List | แสดงรายการ FTT ทั้งหมดในรูปแบบ Table พร้อม Filter |
| L2 | Transaction Detail | แสดงรายละเอียด FTT และ Bank Step Log เมื่อคลิก Row ใน L1 |

> L2 เปิดจาก L1 — User คลิก Row ใน Table เพื่อดูรายละเอียด

---

## Display Condition (Role-Based)

| Role | เห็น FTT สถานะ |
|------|--------------|
| Maker | FTT ที่ตัวเองสร้าง (FTR ของตัวเอง) |
| Checker | FTT ทั้งหมดที่ผ่าน PENDING_CHECKER |
| Signer | FTT ทั้งหมดที่ผ่าน PENDING_SIGNER |
| Ops Manager | FTT ทั้งหมด |

> **TBD** — Role visibility scope ยังรอการยืนยันจาก PO

---

## Main Functions

| # | Function | File |
|---|----------|------|
| 1 | Transaction History List | [D4_F1_TransactionHistory.md](D4_F1_TransactionHistory.md) |
| 2 | Transaction Detail | [D4_F2_TransactionDetail.md](D4_F2_TransactionDetail.md) |

---

## FTT States ที่แสดงใน D4

| State | ความหมาย |
|-------|---------|
| `PENDING_CHECKER` | รอ Checker ตรวจสอบ |
| `PENDING_SIGNER` | รอ Signer อนุมัติ |
| `BANK_SUBMITTED` | ส่งธนาคารแล้ว (สำเร็จ) |
| `REJECTED` | ถูกยกเลิก |
