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
| Signer | `PENDING_SIGNER` | `BANK_SUBMITTED` (สำเร็จ) / `PENDING_SIGNER` (ล้มเหลว) | ระบบส่งคำสั่งโอนเงินไปธนาคาร — แสดง **Spinner** ระหว่างรอผลตอบกลับ |

### Loading State (Signer เท่านั้น)

| Step | State | Display |
|------|-------|---------|
| Signer กดยืนยันใน Modal | รอธนาคารตอบกลับ | แสดง **Spinning / Loading indicator** — Block UI ไม่ให้กดซ้ำ |
| ธนาคารตอบ: สำเร็จ | `BANK_SUBMITTED` | Spinner หาย → FTT หายจาก D3 |
| ธนาคารตอบ: ล้มเหลว | `PENDING_SIGNER` (คงเดิม) | Spinner หาย → แสดง Section **ผลการโอนเงินจากธนาคาร** ใน Panel 3 |

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
