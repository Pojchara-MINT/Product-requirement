# D3 — F2: Review & Decide (Approve / Reject)

> **Display:** D3 | **Function:** 2 of 3
> **Back to overview:** [D3_Overview.md](D3_Overview.md)

---

**Objective:** User (Checker / Signer) ตรวจสอบรายละเอียด FTT เปรียบเทียบกับเอกสาร แล้วตัดสินใจ Approve หรือ Reject

---

## Flow

| Step | Action | System Response |
|------|--------|----------------|
| 1 | User เลือก FTT Card ใน Panel 1 | Panel 2 โหลดเอกสาร / Panel 3 โหลดรายละเอียด |
| 2 | User อ่านรายละเอียดใน Panel 3 เปรียบเทียบกับเอกสารใน Panel 2 | — |
| 3 | User กดปุ่ม **ยืนยัน** หรือ **ยกเลิก** | ระบบแสดง Confirmation Modal |
| 4 | User กดยืนยันใน Modal | ระบบอัปเดต FTT state → FTT หายจาก D3 |

---

## Approve Flow

### กดปุ่ม ยืนยัน

ระบบตรวจสอบ **Approval-time Validation** ก่อนแสดง Modal — ดูรายละเอียด Rule ใน [D3_F4_ValidationStatus.md](D3_F4_ValidationStatus.md)

#### กรณี Validation ผ่าน

| | |
|--|--|
| **Modal Wording** | **"ต้องการยืนยันรายการนี้หรือไม่"** |
| **Buttons** | ยกเลิก (แดง) / ยืนยัน (เขียว) |

#### กรณี Validation ไม่ผ่าน

| | |
|--|--|
| **Modal Wording** | Error message (เช่น "สถานะสัญญานี้ปิดแล้ว") |
| **Buttons** | ปิด Modal |
| **ผลหลัง Modal ปิด** | ปุ่ม ยืนยัน **หายออกจาก Panel 3** — เหลือเฉพาะปุ่ม ยกเลิก |

### Expected Result เมื่อ Approve

| Role | State Before | State After | Side Effect |
|------|-------------|------------|-------------|
| Checker | `PENDING_CHECKER` | `PENDING_SIGNER` | FTT หายจาก D3 ของ Checker → เข้า D2 Pool ให้ Signer จอง |
| Signer | `PENDING_SIGNER` | `TRANSFER_SUCCESS` (Step 2 success) / `PENDING_BANK` (Step 2 code 9999) / `PENDING_SIGNER` (Step 2 other fail) | ระบบส่งคำสั่งโอนเงินไปธนาคาร — แสดง **Spinner** ระหว่างรอผลตอบกลับ |

### TP7 — rsTransID Freshness Check (Signer เท่านั้น)

ก่อนระบบส่ง Bank Step 2 (Fund Transfer Submit) ระบบตรวจสอบความสดใหม่ของ rsTransID ที่ได้รับจาก Bank Step 1 (Account Inquiry) อัตโนมัติ

| ผล TP7 | Action ของระบบ | User เห็นอะไร |
|--------|---------------|--------------|
| rsTransID **ยังไม่หมดอายุ** | ส่ง Bank Step 2 ทันที | Spinner (ดูด้านล่าง) |
| rsTransID **หมดอายุแล้ว** | Re-run Bank Step 1 (Account Inquiry) อัตโนมัติเพื่อรับ rsTransID ใหม่ → จากนั้นส่ง Bank Step 2 | Spinner ต่อเนื่อง (User ไม่ต้องทำอะไรเพิ่ม) |

> ระบบจัดการ TP7 และ re-inquiry อัตโนมัติทั้งหมด — User เห็นเพียง Spinner ระหว่างรอผล

---

### Loading State (Signer เท่านั้น)

| Step | State | Display |
|------|-------|---------|
| Signer กดยืนยันใน Modal | รอธนาคารตอบกลับ | แสดง **Spinning / Loading indicator** — Block UI ไม่ให้กดซ้ำ |
| Bank Step 2 สำเร็จ | `TRANSFER_SUCCESS` | Spinner หาย → FTT หายจาก D3 |
| Bank Step 2 Fail code 9999 | `PENDING_BANK` | Spinner หาย → FTT หายจาก D3 (Worker จะ Poll Step 3 ต่อ) |
| Bank Step 2 Fail other code | `PENDING_SIGNER` (คงเดิม) | Spinner หาย → แสดง Section **ผลการโอนเงินจากธนาคาร** ใน Panel 3 พร้อม Error Code |

---

## Retry Flow

### เงื่อนไขที่แสดงปุ่ม Retry

ดูตาราง Action Button ใน [D3_F1_DisplayMyTasks.md](D3_F1_DisplayMyTasks.md) — ปุ่ม Retry แสดงเมื่อ:
- ผลตรวจสอบบัญชีปลายทาง = ยังไม่ได้รับผล (รอผลจากธนาคาร)
- ผลตรวจสอบบัญชีปลายทาง = ไม่ผ่าน และเป็น Retryable Response Code
- ผลเงื่อนไขโอนเงิน = ยังไม่ได้รับผล (แม้ Precheck จะผ่านแล้ว)

### Retry Action

| Step | Action | System Response |
|------|--------|----------------|
| 1 | User กดปุ่ม **Retry** | ระบบแสดง Confirmation Modal: "ต้องการส่งคำขอตรวจสอบบัญชีปลายทางใหม่หรือไม่" |
| 2 | User กด **ยืนยัน** ใน Modal | ระบบ re-run **Bank Step 1 (Verify Account)** + **M4 Internal Rule Check** ทันที |
| 3 | รอผลตอบกลับ | แสดง Spinner — Block UI ไม่ให้กดซ้ำ |
| 4 | ได้รับผลใหม่ | Spinner หาย → Section ผลตรวจสอบบัญชีปลายทาง อัปเดต → ปุ่มเปลี่ยนตาม Action Button Logic |

> Retry = manual re-trigger เทียบเท่า TP4 (Bank Step 1 + M4) — ระบบดึงผลใหม่จากธนาคารและประเมินกฎภายในใหม่ทั้งหมด

### Expected Result หลัง Retry

| ผลใหม่ | State | ปุ่มที่แสดง |
|--------|-------|-----------|
| ผ่านทั้งคู่ | คงเดิม (PENDING_CHECKER / PENDING_SIGNER) | ยกเลิก + ยืนยัน |
| Precheck ผ่าน, BV ไม่ผ่าน | คงเดิม | ยกเลิก + ยืนยัน |
| Precheck ผ่าน, BV ยังไม่ได้รับผล | คงเดิม | ยกเลิก + Retry |
| Precheck ไม่ผ่าน (Retryable) | คงเดิม | ยกเลิก + Retry |
| Precheck ไม่ผ่าน (Non-retryable) | คงเดิม | ยกเลิก อย่างเดียว |
| ยังไม่ได้รับผล (Timeout / ธนาคารไม่ตอบ) | คงเดิม | ยกเลิก + Retry |

> **Non-retryable Response Codes:** TBD — รอข้อมูลจาก Dev / ธนาคาร (ดู [D3_F4_ValidationStatus.md](D3_F4_ValidationStatus.md))

---

## Reject Flow

### กดปุ่ม ยกเลิก

ระบบเปิด **Rejection Panel** ใน Panel 3 (ไม่ใช่ Modal แยก) แสดงฟอร์มดังนี้:

### Rejection Form Fields

| Field | Type | M/O | Description |
|-------|------|-----|-------------|
| เหตุผล | Multi-select Dropdown | M | เลือกได้หลายตัวเลือก |
| หมายเหตุ | Text Area | O | แสดงทันทีเมื่อเลือกเหตุผลอย่างน้อย 1 ข้อ |

### เหตุผลการยกเลิก (Rejection Reason Options)

| # | ตัวเลือก |
|---|---------|
| 1 | หมายเลขบัญชีผิด |
| 2 | ธนาคารผิด |
| 3 | บัญชีปิด |
| 4 | ไม่ได้ผูกพร้อมเพย์ |
| 5 | ไม่สามารถโอนเงินได้ |
| 6 | ชื่อสกุลไม่ตรง แนบใบเปลี่ยนชื่อสกุลเพิ่มเติม |
| 7 | อัปเอกสารผิดประเภท |
| 8 | อื่นๆ |

### Dynamic Field Rule

| Condition | Action |
|-----------|--------|
| ยังไม่ได้เลือกเหตุผล | ไม่แสดง Field หมายเหตุ |
| เลือกเหตุผลอย่างน้อย 1 ข้อ | **แสดง Field หมายเหตุ ทันที** (Optional) |

> Placeholder หมายเหตุ: *"กรุณาระบุหมายเหตุ..."*

### Flow

| Step | Action | System Response |
|------|--------|----------------|
| 1 | User กดปุ่ม **ยกเลิก** | แสดง Rejection Form ใน Panel 3 |
| 2 | User เลือก เหตุผล (Multi-select) | Field **หมายเหตุ** ปรากฏทันที |
| 3 | User กรอก หมายเหตุ | — |
| 4 | User กด **ยืนยัน** | ระบบบันทึก Rejection + ส่งเหตุผลกลับไปยัง LOS |
| 5 | — | FTT State → `REJECTED` / FTT หายจาก D3 |

> **วัตถุประสงค์:** เหตุผลและหมายเหตุจะถูกส่งกลับไปยัง **LOS System** เพื่อให้ Branch Officer อ่านและแก้ไขก่อน Re-submit

### Validation

| Field | Rule | Error Message |
|-------|------|---------------|
| เหตุผล | ต้องเลือกอย่างน้อย 1 ข้อ | — |
| หมายเหตุ | Optional | — |

### Expected Result เมื่อ Reject

| Role | State Before | State After | Side Effect |
|------|-------------|------------|-------------|
| Checker | `PENDING_CHECKER` | `REJECTED` | FTT หายจาก D3 |
| Signer | `PENDING_SIGNER` | `REJECTED` | FTT หายจาก D3 |

---

## Display Rule After Decision

> หลังจาก Approve หรือ Reject แล้ว — FTT จะ**ไม่ปรากฏ**ใน D3 ของ User คนนี้อีก
> Panel 2 และ Panel 3 จะว่างเปล่า หรือโหลด FTT ถัดไป (ถ้ามี)
