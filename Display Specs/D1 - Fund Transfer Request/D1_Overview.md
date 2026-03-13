# D1 — Fund Transfer Request (Overview)

> **Feature:** F3 — Fund Transfer Request
> **Version:** 0.2 Draft
> **Last Revised:** 2026-03-11
> **Primary Actor:** Maker / Checker

---

## Page Layout

```
┌────────────────────┬───────────────────────────┬──────────────────────┐
│  Panel 1           │  Panel 2                  │  Panel 3             │
│  FTR Card List     │  Document Viewer          │  FTR Detail          │
│  (Left)            │  (Middle)                 │  (Right)             │
└────────────────────┴───────────────────────────┴──────────────────────┘
```

**Top Navigation Tabs:** สร้างรายการใหม่ | งานของฉัน | งานที่หมด | สถานะงาน

**Top Action Buttons:** กรองข้อมูล | + เพิ่มรายการใหม่ | อัปโหลดรายการโอนเงิน

---

## Main Functions

| # | Function | File | Description |
|---|----------|------|-------------|
| 1 | Display All Fund Transfer Requests | [D1_F1_DisplayAllFTR.md](D1_F1_DisplayAllFTR.md) | แสดงรายการ FTR ในรูปแบบ 3-Panel Layout |
| 2 | Single Request (Manual Create) | [D1_F2_SingleRequest.md](D1_F2_SingleRequest.md) | กรอกฟอร์มสร้างรายการทีละรายการ |
| 3 | Batch Upload | [D1_F3_BatchUpload.md](D1_F3_BatchUpload.md) | อัปโหลดไฟล์ CSV เพื่อสร้างหลายรายการพร้อมกัน |
| 4 | Filter Fund Transfer Request | [D1_F4_Filter.md](D1_F4_Filter.md) | ค้นหาและกรองรายการ |
| 5 | Approve & Reject FTR | [D1_F5_Approve_Reject.md](D1_F5_Approve_Reject.md) | อนุมัติหรือปฏิเสธรายการ FTR |

---

## State Transitions (D1 scope)

| Action | State Before | State After | Side Effect |
|--------|-------------|------------|-------------|
| Create (ยืนยัน Modal) | — | `PENDING_MAKER` | FTR record สร้างขึ้น |
| Batch Upload (row valid) | — | `PENDING_MAKER` | 1 FTR ต่อ 1 row |
| Approve | `PENDING_MAKER` | `FTR_APPROVED` | FTT สร้างขึ้น (state: `PENDING_CHECKER`) เข้าคิว Checker บน D2 — Card หายออกจาก D1 |
| Reject | `PENDING_MAKER` | `FTR_REJECTED` | ปิดถาวร ไม่มี FTT สร้างขึ้น — Card หายออกจาก D1 |
