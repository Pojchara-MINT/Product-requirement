# API — FTR Auto-Submission (Straight-Through Processing)

> **Feature:** F3 — Fund Transfer Request (Task 5)
> **Type:** API — ไม่มี UI / ไม่ใช่ Display
> **Version:** 0.1 Draft
> **Last Revised:** 2026-03-11

---

## Overview

ระบบรองรับการสร้าง Fund Transfer Request จาก **ระบบภายนอกที่น่าเชื่อถือ (Trusted Source System)** ผ่าน API โดยสามารถสร้างและ Auto-Approve ได้ใน API Call เดียว โดยไม่ต้องให้ Maker ทำงานใน UI

---

## Use Cases

### Happy Path

| Step | Description |
|------|-------------|
| 1 | Source system ส่ง API Request พร้อม Credentials และ Auto-Approve flag |
| 2 | ระบบ Validate credentials → ผ่าน |
| 3 | ระบบสร้าง FTR (state: `FTR_APPROVED`) และสร้าง FTT (state: `PENDING_CHECKER`) ใน Call เดียว |
| 4 | FTT เข้าคิว Checker บน D2 ทันที |
| 5 | API Response: สำเร็จ พร้อม FTR ID และ FTT ID |

### Unhappy Path

| Case | Expected Result |
|------|----------------|
| Credentials ไม่ถูกต้อง | Request ถูก Reject — ไม่มีการสร้าง FTR หรือ FTT |
| Source system ส่ง Request ซ้ำ (retry) | ระบบตรวจจับเป็น Duplicate — Return existing FTR โดยไม่สร้างรายการใหม่ |

---

## Notes

- Feature นี้ไม่มี UI บน D1 — ผลลัพธ์ (FTT) จะปรากฏบน D2 (Checker Queue) โดยตรง
- การออกแบบ API endpoint, payload format, และ authentication method เป็นความรับผิดชอบของ Dev Team
