# D3 — F3: คืนงาน (Return to Pool)

> **Display:** D3 | **Function:** 3 of 3
> **Back to overview:** [D3_Overview.md](D3_Overview.md)

---

**Objective:** User คืน FTT ที่จองไว้กลับเข้า Pool (D2) โดยไม่ได้ตัดสินใจ Approve หรือ Reject

---

## Flow

| Step | Action | System Response |
|------|--------|----------------|
| 1 | User กดปุ่ม **คืนงาน** บน FTT Card | ระบบแสดง Confirmation Modal |
| 2 | User กดยืนยัน | FTT ถูกคืนกลับเข้า Pool → หายจาก D3 ของ User → ปรากฏใน D2 งานทั้งหมดอีกครั้ง (ผู้ตรวจ = `-`) |

---

## Modal

| | |
|--|--|
| **Modal Wording** | **"ต้องการคืนงานนี้หรือไม่"** |
| **Buttons** | ยกเลิก (แดง) / ยืนยัน (เขียว) |
| **Expected Result (ยืนยัน)** | FTT ถูก unassign → กลับเข้า D2 Pool — หายจาก D3 ของ User คนนี้ |
| **Expected Result (ยกเลิก)** | Modal ปิด — ไม่มีการเปลี่ยนแปลง |

---

## State Transition

| Action | State Before | State After | assigned_to |
|--------|-------------|------------|-------------|
| คืนงาน (Checker) | `PENDING_CHECKER` (assigned) | `PENDING_CHECKER` (unassigned) | ถูกลบออก → `-` |
| คืนงาน (Signer) | `PENDING_SIGNER` (assigned) | `PENDING_SIGNER` (unassigned) | ถูกลบออก → `-` |

---

## Display Rule After Return

> FTT ที่คืนแล้วจะ**ไม่ปรากฏ**ใน D3 ของ User คนนี้
> FTT จะกลับไปปรากฏใน **D2 — งานทั้งหมด** ใน Pool ให้ผู้อื่นจองได้ใหม่
