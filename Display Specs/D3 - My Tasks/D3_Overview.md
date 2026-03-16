# D3 — My Tasks / งานของฉัน (Overview)

> **Feature:** F4 — FTT Main Structure + D3 + Review + Decision
> **Version:** 0.1 Draft
> **Last Revised:** 2026-03-11
> **Primary Actor:** Checker, Signer

---

## Page Layout

3-Panel Layout — ผู้ตรวจรีวิว FTT และตัดสินใจอนุมัติหรือยกเลิก

**Top Navigation Tabs:** สร้างรายการใหม่ | **งานของฉัน** | งานทั้งหมด | สถานะงาน

| Panel | ชื่อ | ตำแหน่ง | หน้าที่ |
|-------|------|---------|---------|
| Left | FTT Card List | ซ้าย | แสดงรายการ FTT ที่ User จองไว้ |
| Middle | Document Viewer | กลาง | แสดงเอกสารประกอบของ FTT ที่เลือก |
| Right | FTT Detail | ขวา | แสดงรายละเอียด FTT + ช่องทางตัดสินใจ |

---

## Display Condition (Role-Based)

| Role | เห็น FTT สถานะ | หน้าที่ |
|------|--------------|---------|
| Checker | `PENDING_CHECKER` ที่ตัวเองจองเท่านั้น | ตรวจสอบและ Approve → PENDING_SIGNER / Reject → REJECTED |
| Signer | `PENDING_SIGNER` ที่ตัวเองจองเท่านั้น | ตรวจสอบและ Approve → TRANSFER_SUCCESS / PENDING_BANK / PENDING_SIGNER (stay) / Reject → REJECTED |

> แสดงเฉพาะ FTT ที่ User คนนี้เป็นผู้จอง (assigned_to = current_user)

---

## Main Functions

| # | Function | File |
|---|----------|------|
| 1 | Display My Tasks | [D3_F1_DisplayMyTasks.md](D3_F1_DisplayMyTasks.md) |
| 2 | Review & Decide (Approve / Reject) | [D3_F2_ReviewAndDecide.md](D3_F2_ReviewAndDecide.md) |
| 3 | คืนงาน (Return to Pool) | [D3_F3_ReturnToPool.md](D3_F3_ReturnToPool.md) |
| 4 | Validation Status Display | [D3_F4_ValidationStatus.md](D3_F4_ValidationStatus.md) |

---

## State Transitions (D3 scope)

| Action | Role | State Before | State After | Side Effect |
|--------|------|-------------|------------|-------------|
| Approve | Checker | `PENDING_CHECKER` | `PENDING_SIGNER` | FTT หายจาก D3 ของ Checker → เข้า D2 Pool ของ Signer |
| Approve (Bank Step 2 สำเร็จ) | Signer | `PENDING_SIGNER` | `TRANSFER_SUCCESS` | FTT หายจาก D3 ของ Signer |
| Approve (Bank Step 2 Fail code 9999) | Signer | `PENDING_SIGNER` | `PENDING_BANK` | FTT หายจาก D3 ของ Signer — Worker เริ่ม Poll Bank Step 3 |
| Approve (Bank Step 2 Fail other code) | Signer | `PENDING_SIGNER` | `PENDING_SIGNER` (คงเดิม) | FTT ยังอยู่ใน D3 — แสดง Section **ผลการโอนเงินจากธนาคาร** พร้อม Error Code. Signer กด Re-submit (retry Step 2) หรือ Reject ได้ |
| Approval-time Validation ไม่ผ่าน | Checker / Signer | `PENDING_CHECKER` / `PENDING_SIGNER` | ไม่เปลี่ยน | Error Modal แสดง → ปุ่ม ยืนยัน หาย → เหลือเฉพาะ ยกเลิก |
| Reject | Checker / Signer | `PENDING_CHECKER` / `PENDING_SIGNER` | `REJECTED` | FTT หายจาก D3 |
| คืนงาน | Checker / Signer | `PENDING_CHECKER` / `PENDING_SIGNER` (assigned) | `PENDING_CHECKER` / `PENDING_SIGNER` (unassigned) | FTT กลับเข้า D2 Pool — หายจาก D3 ของ User นี้ |
