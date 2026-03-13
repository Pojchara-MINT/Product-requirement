# D1 — F5: Approve & Reject FTR

> **Display:** D1 | **Function:** 5 of 5
> **Back to overview:** [D1_Overview.md](D1_Overview.md)

---

## Approve FTR

**Objective:** Maker อนุมัติรายการ FTR → ระบบสร้าง FTT เข้าคิว Checker

### Precondition

| Condition | Requirement |
|-----------|------------|
| FTR State | ต้องอยู่ในสถานะ `PENDING_MAKER` |
| สมุดบัญชี | ต้องแนบไฟล์อย่างน้อย 1 ไฟล์ (Mandatory document) |

### Flow

| Step | Action | System Response |
|------|--------|----------------|
| 1 | Maker เลือก FTR (Checkbox) แล้วกดปุ่ม **"ยืนยันสร้างรายการ"** | ระบบแสดง Confirmation dialog: *"ยืนยันการอนุมัติรายการนี้?"* |
| 2 | Maker กดยืนยัน | FTR state → `FTR_APPROVED` ระบบสร้าง FTT (state: `PENDING_CHECKER`) FTT เข้าคิว Checker บน D2 Toast: *"อนุมัติรายการสำเร็จ"* |
| 3 | Card หายออกจาก List | FTR ไม่อยู่ในสถานะ `PENDING_MAKER` แล้ว → Card ถูกลบออกจาก Panel 1 ทันที |

---

## Reject FTR

**Objective:** Maker ปฏิเสธรายการ FTR — ปิดรายการถาวร ไม่สามารถดำเนินการต่อได้

### Flow

| Step | Action | System Response |
|------|--------|----------------|
| 1 | Maker เลือก FTR (Checkbox) แล้วกดปุ่ม **"ยกเลิกรายการ"** | ระบบแสดง Modal พร้อม: Rejection Reason (Dropdown — Mandatory) / Additional Note (Textarea — Optional, max 200 chars) |
| 2 | Maker เลือกเหตุผลและกดยืนยัน | FTR state → `FTR_REJECTED` (terminal) ไม่มีการสร้าง FTT Toast: *"ปฏิเสธรายการสำเร็จ"* |
| 3 | Card หายออกจาก List | FTR ไม่อยู่ในสถานะ `PENDING_MAKER` แล้ว → Card ถูกลบออกจาก Panel 1 ทันที ไม่สามารถดำเนินการใดๆ กับรายการนี้ได้อีก |

### Rejection Reason Options

| Code | Label |
|------|-------|
| WRONG_BENEFICIARY | ข้อมูลผู้รับโอนไม่ถูกต้อง |
| WRONG_AMOUNT | จำนวนเงินไม่ถูกต้อง |
| DUPLICATE | รายการซ้ำ |
| OTHER | อื่นๆ |

> **หมายเหตุ:** หากเลือก OTHER ต้องกรอก Additional Note
