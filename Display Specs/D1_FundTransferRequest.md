# D1 — Fund Transfer Request

> **Feature:** F3 — Fund Transfer Request
> **Version:** 0.2 Draft
> **Last Revised:** 2026-03-11
> **Primary Actor:** Maker

---

## Main Functions

| # | Function | Description |
|---|----------|-------------|
| 1 | Display All Fund Transfer Requests | แสดงรายการคำขอโอนเงินทั้งหมด พร้อมสถานะ |
| 2 | Single Request | กรอกฟอร์มทีละรายการ |
| 3 | Batch Upload | การอัปโหลดไฟล์แบบชุด |
| 4 | Filter Fund Transfer Request | ค้นหาและกรองรายการ |
| 5 | Approve FTR | อนุมัติ FTR — สร้าง FTT เข้าคิว Checker |
| 6 | Reject FTR | ปฏิเสธ FTR — ปิดรายการถาวร ไม่สามารถดำเนินการต่อได้ |

---

## Function 1: Display All Fund Transfer Requests

**Objective:** แสดงรายการคำขอโอนเงิน (FTR) ทั้งหมดในรูปแบบตาราง เพื่อให้ Maker เห็นภาพรวมของรายการที่สร้าง และสถานะปัจจุบัน

### Display Columns

| Column | Description |
|--------|-------------|
| Request ID | รหัส FTR ที่ระบบสร้างอัตโนมัติ |
| ช่องทาง | ธนาคารปลายทาง |
| เลขที่บัญชี / พร้อมเพย์ | หมายเลขบัญชีหรือ Proxy |
| ชื่อผู้รับ | ชื่อ - สกุล (ภาษาอังกฤษ) |
| จำนวนเงินสุทธิ | ยอดโอน (THB) |
| เลขที่อ้างอิง | Reference Number |
| สถานะ | PENDING_MAKER / FTR_APPROVED / FTR_REJECTED |
| วันที่สร้าง | วันและเวลาที่ Maker สร้างรายการ |
| Action | ปุ่มดำเนินการ (Approve / Reject / View) ขึ้นอยู่กับสถานะ |

---

## Function 2: Single Request (Manual Create)

**Objective:** Maker สามารถสร้างรายการคำขอโอนเงิน (Fund Transfer Request) ทีละรายการโดยสามารถ Input ข้อมูลได้

---

### Step 1 — เปิด Modal สร้างรายการ

Maker กดปุ่ม **"+ เพิ่มรายการ"** → ระบบแสดง Modal ให้ Maker กรอกข้อมูล

---

### Step 2 — กรอกข้อมูลใน Modal

| Field Name | M / O | Validation | Example |
|------------|-------|-----------|---------|
| ช่องทาง | M | 1. ต้องเลือกธนาคาร ไม่สามารถเว้นว่างได้ 2. ค่าที่เลือกต้องเป็นธนาคารที่มีอยู่ในระบบ (Dropdown) | กสิกรไทย |
| เลขที่บัญชี / พร้อมเพย์ | M | 1. ต้องไม่เป็นค่าว่าง 2. ตัวเลขเท่านั้น (ระบบไม่รับตัวอักษร) | 7235612031 |
| ชื่อ - สกุล | O | 1. ต้องไม่เป็นค่าว่าง 2. พิมพ์ได้เฉพาะภาษาไทยเท่านั้น | ทดสอบ ระบบ |
| ชื่อ - สกุล (ภาษาอังกฤษ) | M | 1. ต้องไม่เป็นค่าว่าง 2. พิมพ์ได้เฉพาะภาษาอังกฤษเท่านั้น | Test System |
| จำนวนเงินสุทธิ | M | 1. ต้องไม่เป็นค่าว่าง 2. ตัวเลขเท่านั้น 3. ต้องมากกว่า 0 4. ทศนิยมไม่เกิน 2 ตำแหน่ง 5. แต่ละรายการไม่เกิน 500,000 | 500000 |
| เลขที่อ้างอิง | M | 1. ต้องไม่เป็นค่าว่าง | 1 |
| หมายเหตุ | O | — | Remark |
| สมุดบัญชี | M | 1. ต้องแนบไฟล์อย่างน้อย 1 ไฟล์ 2. รองรับเฉพาะไฟล์ JPG, PNG, PDF | — |
| เอกสารที่ 1 | O | 1. ไม่บังคับแนบ 2. รองรับเฉพาะไฟล์ JPG, PNG, PDF | — |

---

### Step 3 — กดปุ่มยืนยัน

Maker ตรวจสอบข้อมูลและกดปุ่ม **"ยืนยัน"**

---

### Use Cases

#### Case 1 — กรอกข้อมูลไม่ครบ Mandatory Field

| | |
|--|--|
| **Trigger** | Maker กดปุ่มยืนยันโดยที่มี Mandatory Field ที่ยังไม่ได้กรอก |
| **Expected Result** | ระบบแสดงข้อความแจ้งเตือนใต้ Field ที่ผิดพลาดแต่ละช่อง Modal ยังคงเปิดอยู่ ไม่มีการสร้างรายการ |
| **Error Display** | Inline error ใต้แต่ละ Field ที่ไม่ผ่าน validation เช่น *"กรุณากรอกจำนวนเงิน"* |

#### Case 2 — กรอกข้อมูลครบถ้วน

| | |
|--|--|
| **Trigger** | Maker กรอกข้อมูลครบทุก Mandatory Field และกดปุ่มยืนยัน |
| **Expected Result** | ระบบสร้างรายการ FTR สำเร็จ (สถานะ `PENDING_MAKER`) Modal ปิด รายการใหม่ปรากฏที่ด้านบนของตารางในหน้า D1 แสดง Toast notification: *"สร้างรายการสำเร็จ"* |

---

## Function 3: Batch Upload (อัปโหลดรายการโอนเงิน)

**Objective:** Checker สามารถสร้างรายการคำขอโอนเงิน (Fund Transfer Request) แบบที่สามารถวางไฟล์ CSV เพื่อสร้างได้หลายรายการพร้อมกัน

---

### Step 1 — เปิด Modal อัปโหลด

Checker กดปุ่ม **"อัพโหลดรายการโอนเงิน"** → ระบบแสดง Modal ให้ Checker อัพโหลดไฟล์

---

### Step 2 — วางไฟล์ CSV

Checker วางไฟล์ CSV ตาม Column ดังต่อไปนี้:

| Field Name | Check Header | M / O | Condition |
|------------|:---:|-------|-----------|
| ลำดับที่ | | M | — |
| ช่องทาง | ✓ | M | ข้อมูลต้องเป็น `ธนาคาร` หรือ `พร้อมเพย์` เท่านั้น |
| เลขพร้อมเพย์ | ✓ | M if ช่องทาง = พร้อมเพย์ / Null if ช่องทาง = ธนาคาร | — |
| เลขที่บัญชี | ✓ | M if ช่องทาง = ธนาคาร / Null if ช่องทาง = พร้อมเพย์ | ถ้า ช่องทาง = ธนาคาร: 1. เลขบัญชีมีจำนวนหลักตามที่ธนาคารกำหนด 2. เลขบัญชีเป็นตัวเลข |
| bank code | ✓ | M if ช่องทาง = ธนาคาร / Null if ช่องทาง = พร้อมเพย์ | ถ้า ช่องทาง = ธนาคาร: bank code ต้องมีอยู่บนระบบ |
| ธนาคาร | | O | — |
| ชื่อ-นามสกุล | | O | — |
| ชื่อ-นามสกุล (ภาษาอังกฤษ) | ✓ | M | ต้องเป็นภาษาอังกฤษเท่านั้น |
| จำนวนเงิน | ✓ | M | 1. เป็นจำนวนจริง (ทศนิยม 2 ตำแหน่ง) 2. ต้องเป็นตัวเลข 3. แต่ละรายการไม่เกิน 500,000 |
| เลขที่อ้างอิง | ✓ | M | — |
| หมายเหตุ | | O | — |
| สมุดบัญชี/บัตรประชาชน | ✓ | M | — |

> **Check Header = ✓** หมายถึงระบบตรวจสอบว่า Header ชื่อนี้มีอยู่ในไฟล์ หากไม่พบจะแจ้งเตือน

#### Use Cases — การวางไฟล์

**Case 1: User วางไฟล์ที่ไม่ใช่ Format CSV**

| | |
|--|--|
| **Expected Result** | ระบบขึ้น Pop-up แจ้งเตือน: **"ผิดพลาด! ไฟล์ที่จะ upload ต้องเป็น .csv เท่านั้น!"** |

**Case 2: User วางไฟล์ CSV แต่ไม่พบ Required Header**

| | |
|--|--|
| **Expected Result** | ระบบขึ้น Pop-up แจ้งเตือน: **"ผิดพลาด! กรุณาระบุข้อมูล `field name`, `field name`"** (ระบุชื่อ Header ที่หายไป) |

**Case 3: User วางไฟล์ CSV และมี Required Header ครบ**

| | |
|--|--|
| **Expected Result** | ระบบอัพโหลดไฟล์สำเร็จ แล้วแสดงชื่อไฟล์ที่อัพโหลดใน Modal |

---

### Step 3 — กดปุ่มยืนยัน

Checker กดปุ่ม **"ยืนยัน"** เพื่อยืนยันการสร้างรายการ

#### Use Cases — การยืนยัน

**Case 1: ข้อมูลทุก row ตรงตาม Condition**

| | |
|--|--|
| **Expected Result** | ระบบขึ้น Modal สรุปรายการและยอดเงินทั้งหมด |

**Case 2: มีข้อมูลบางหรือทุก row ไม่ตรงตาม Condition**

| | |
|--|--|
| **Expected Result** | ระบบขึ้น Modal แจ้งเตือน: **"อัปโหลดรายการผิดพลาด N รายการ กรุณาดาวน์โหลดเอกสารเพื่อตรวจสอบรายการผิดพลาด"** พร้อม Auto download Validation File |

---

### Validation File

**Format ชื่อไฟล์:** `{original filename}-validate.csv`

**Columns:**

| ลำดับที่ | ช่องทาง | เลขพร้อมเพย์ | เลขที่บัญชี | bank code | ธนาคาร | ชื่อ-นามสกุล | ชื่อ-นามสกุล (ภาษาอังกฤษ) | จำนวนเงิน | เลขที่อ้างอิง | หมายเหตุ | สมุดบัญชี/บัตรประชาชน | error message |
|---|---|---|---|---|---|---|---|---|---|---|---|---|

**ตัวอย่าง row ที่ผ่าน validation:**

| 1 | พร้อมเพย์ | 1309902780589 | | | | UATname2 | two two | 25000 | 3000000001 | Remark | https://…/image.png | (ว่าง) |

---

### Validation Wording

| Case | Error Message |
|------|--------------|
| Required field เป็นค่าว่าง | กรุณาระบุ '{field name}' |
| เลขบัญชีไม่ตรงตาม format ของ bank | account number ไม่ตรงตาม format ของ bank |
| ชื่อ-นามสกุล (ภาษาอังกฤษ) ไม่เป็นภาษาอังกฤษ | ชื่อ-นามสกุล(ภาษาอังกฤษ) จะต้องเป็นภาษาอังกฤษ |
| ทศนิยมเกิน 2 ตำแหน่ง | ทศนิยมต้องไม่เกิน 2 ตัว |
| จำนวนเงินติดลบ | รูปแบบของจำนวนเงินไม่ถูกต้อง |
| จำนวนเงินมากกว่า 500,000 | จำนวนเงินต้องมากกว่า 0 และไม่เกิน 500000 |

---

### Bank Code & Account Number Format

| Bank Code | Valid Account Number Length (digits) |
|-----------|-------------------------------------|
| 002 | 10 |
| 004 | 10 |
| 006 | 10 |
| 011 | 10 |
| 014 | 10 |
| 020 | 10 |
| 022 | 10 |
| 024 | 10 |
| 025 | 10 |
| 026 | 10, 11, 12, 13, 14, 15 |
| 030 | 12, 15 |
| 033 | 12 |
| 034 | 12 |
| 035 | 10, 11, 12, 13, 14, 15 |
| 052 | 10, 11, 12, 13, 14, 15 |
| 066 | 10 |
| 067 | 14 |
| 069 | 14 |
| 070 | 10 |
| 071 | 10, 11, 12, 13, 14, 15 |
| 073 | 10 |
| 079 | 10, 11, 12, 13, 14, 15 |
| 080 | 10, 11, 12, 13, 14, 15 |
| 098 | 10, 11, 12, 13, 14, 15 |

---

## Function 4: Filter Fund Transfer Request

**Objective:** Maker ค้นหาและกรองรายการในตาราง

### Filter Fields

| Filter | Type | Description |
|--------|------|-------------|
| Request ID | Text input | ค้นหาด้วยรหัส FTR |
| ช่องทาง (ธนาคาร) | Dropdown | กรองตามธนาคารปลายทาง |
| เลขที่บัญชี / พร้อมเพย์ | Text input | ค้นหาด้วยหมายเลขบัญชี |
| สถานะ | Dropdown | PENDING_MAKER / FTR_APPROVED / FTR_REJECTED |
| วันที่สร้าง | Date range picker | กรองตามช่วงวันที่ |

---

## Function 5: Approve FTR

**Objective:** Maker อนุมัติรายการ FTR → ระบบสร้าง FTT เข้าคิว Checker

### Precondition

| Condition | Requirement |
|-----------|------------|
| FTR State | ต้องอยู่ในสถานะ `PENDING_MAKER` |
| สมุดบัญชี | ต้องแนบไฟล์อย่างน้อย 1 ไฟล์ (Mandatory document) |

### Flow

| Step | Action | System Response |
|------|--------|----------------|
| 1 | Maker กดปุ่ม **"Approve"** บนรายการที่ต้องการ | ระบบแสดง Confirmation dialog: *"ยืนยันการอนุมัติรายการนี้?"* |
| 2 | Maker กดยืนยัน | FTR state → `FTR_APPROVED` ระบบสร้าง FTT (state: `PENDING_CHECKER`) FTT เข้าคิว Checker บน D2 Toast: *"อนุมัติรายการสำเร็จ Transaction ID: [FTT-XXXX]"* |
| 3 | สถานะในตาราง D1 อัปเดต | แสดงสถานะ `FTR_APPROVED` ปุ่ม Approve / Reject ถูกซ่อน |

---

## Function 6: Reject FTR

**Objective:** Maker ปฏิเสธรายการ FTR — ปิดรายการถาวร ไม่สามารถดำเนินการต่อได้

### Flow

| Step | Action | System Response |
|------|--------|----------------|
| 1 | Maker กดปุ่ม **"Reject"** บนรายการที่ต้องการ | ระบบแสดง Modal พร้อม: Rejection Reason (Dropdown — Mandatory) Additional Note (Textarea — Optional, max 200 chars) |
| 2 | Maker เลือกเหตุผลและกดยืนยัน | FTR state → `FTR_REJECTED` (terminal) ไม่มีการสร้าง FTT Toast: *"ปฏิเสธรายการสำเร็จ"* |
| 3 | สถานะในตาราง D1 อัปเดต | แสดงสถานะ `FTR_REJECTED` ปุ่ม Approve / Reject ถูกซ่อน ไม่สามารถดำเนินการใดๆ กับรายการนี้ได้อีก |

### Rejection Reason Options

| Code | Label |
|------|-------|
| WRONG_BENEFICIARY | ข้อมูลผู้รับโอนไม่ถูกต้อง |
| WRONG_AMOUNT | จำนวนเงินไม่ถูกต้อง |
| DUPLICATE | รายการซ้ำ |
| OTHER | อื่นๆ |

> **หมายเหตุ:** หากเลือก OTHER ต้องกรอก Additional Note

---

## State Transitions (D1 scope)

| Action | State Before | State After | Side Effect |
|--------|-------------|------------|-------------|
| Create (ยืนยัน Modal) | — | `PENDING_MAKER` | FTR record สร้างขึ้น |
| Batch Upload (row valid) | — | `PENDING_MAKER` | 1 FTR ต่อ 1 row |
| Approve | `PENDING_MAKER` | `FTR_APPROVED` | FTT สร้างขึ้น (state: `PENDING_CHECKER`) เข้าคิว Checker บน D2 |
| Reject | `PENDING_MAKER` | `FTR_REJECTED` | ปิดถาวร ไม่มี FTT สร้างขึ้น |
