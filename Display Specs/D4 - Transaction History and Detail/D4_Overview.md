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

## Display Condition

> ทุก Role (Maker, Checker, Signer, Ops Manager) เห็น FTT **ทุกรายการ ทุกสถานะ** ใน D4 เหมือนกัน — ไม่มีการกรองตาม Role

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
| `CREATED` | FTT สร้างแล้ว — Background checks กำลังทำงาน |
| `PENDING_CHECKER` | รอ Checker ตรวจสอบ |
| `PENDING_SIGNER` | Checker อนุมัติแล้ว — รอ Signer อนุมัติ |
| `PENDING_BANK` | Signer อนุมัติแล้ว — Bank Step 2 ตอบ code 9999 (ธนาคารยังไม่ยืนยัน) — Worker กำลัง Poll Step 3 |
| `TRANSFER_SUCCESS` | โอนเงินสำเร็จ — ยืนยันจากธนาคาร (Step 2 หรือ Step 3) |
| `TRANSFER_FAIL` | โอนเงินล้มเหลว — ยืนยันจากธนาคาร (Step 3 หรือ Reconcile T+1) — Ops Manager สามารถ Re-submit หรือ Reject ได้จาก D4 |
| `REJECTED` | ถูกยกเลิกโดย Checker / Signer / Ops Manager |
