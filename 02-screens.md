# Screen design & wireframe specification

## Shell และรูปแบบร่วม

Header แสดง Company/Ledger, OU และงวดที่กำลังทำงานอย่างชัดเจน เปลี่ยนบริษัทแล้วล้างรายการที่เลือกและโหลดสิทธิ์ใหม่ ไม่แสดงยอดรวมข้ามบริษัท Sidebar จัดตามงาน มี global search ที่เคารพ scope และ Approval Inbox

หน้ารายการมีค้นหา/ตัวกรอง/คอลัมน์/เรียงข้อมูล/แบ่งหน้าฝั่ง server หน้ารายละเอียดมี document number, status, owner, date, actions และ tabs: รายการ, เอกสารเชื่อมโยง, บัญชี, แนบไฟล์, ประวัติ การกระทำที่ไม่มีสิทธิ์ต้องปฏิเสธที่ API แม้ซ่อนปุ่มแล้ว

สถานะต้องมีข้อความ ไม่ใช้สีอย่างเดียว ฟอร์มระบุ required, field error และเก็บค่าที่กรอกเมื่อบันทึกไม่ผ่าน ตารางมี empty/loading/error states, amount จัดขวา, รหัสคงเลขศูนย์นำหน้า Dialog trap focus/คืน focus, keyboard navigation; mobile แสดง review/approve เป็นหลัก หลีกเลี่ยงย่อตาราง Desktop จนอ่านไม่ได้

## Screen inventory

| กลุ่ม | ID / หน้าจอ | Action และข้อมูลสำคัญ |
| --- | --- | --- |
| Home | H01 ภาพรวม, H02 Inbox, H03 Search/Document Trace | KPI แยก scope, งานค้าง, ส่งกลับ/อนุมัติ, เส้นทางเอกสาร |
| Setup | S01 Org/Company/OU, S02 Users/Scopes, S03 Workflow, S04 Numbering/Period, S05 Tax/Accounts | version/effective date, maker/checker, audit |
| Master | M01 Customer/Site/Credit, M02 Supplier/Site/Bank, M03 Item/UOM, M04 Price/Terms, M05 Warehouse/Locator | TH/EN, active dates, permission filters |
| Budget | B01 Budget Versions, B02 Budget Detail, B03 Reservation Ledger, B04 Transfer/Increase Request | approved version, original/changes/encumbered/actual/available; P05: actual เมื่อ AP ผ่านบัญชี |
| PO | P01 PR List/Form, P02 PO List/Form, P03 PO Revision, P04 Receiving, P05 Return to Supplier, P06 Match Workbench | quantity ordered/received/returned/invoiced, price variance, receipt destination |
| INV | I01 On-hand, I02 Movements, I03 Reservations, I04 Transfer, I05 Count Session, I06 Count Entry/Recount, I07 Variance Approval, I08 Cost History/Valuation, I09 Org Period | on-hand/reserved/available/in-transit, quantity/value delta; P01/P07 ยืนยันแล้ว |
| AP | A01 AP Invoice, A02 Invoice Matching/Hold, A03 Payment Proposal, A04 Payment, A05 Supplier Aging | partial invoice/payment, available balance, supplier credit/return link |
| OM | O01 Quotation, O02 SO, O03 Credit/Hold Review, O04 Allocation | customer/site/terms, GP approval, outstanding/committed credit |
| Shipping | F01 Delivery Workbench, F02 Pick List, F03 Pack/Shipment, F04 Ship Confirm, F05 POD/Delivery Status | picked/packed/shipped/returned by line, packages/carrier/tracking, P03/P04 ยืนยันแล้ว |
| Billing/AR | R01 Billing Workbench, R02 AR Invoice, R03 Credit/Debit Memo, R04 Receipt/Application, R05 Customer Aging | unbilled shipment quantity, tax snapshot, due date, receipt allocation |
| Collection | C01 Collection Worklist, C02 Customer Case, C03 Billing Batch/Statement, C04 Promise/Dispute | owner, overdue, last contact, promise date/amount, next action, actual payment link |
| FA | FAI01 Capitalization Queue, FAI02 Asset Register/Detail, FAI03 CIP Allocation, FAI04 Depreciation Preview/Run, FAI05 Transfer, FAI06 Physical Count, FAI07 Retirement | AP source, location/custodian, cost/residual/useful life, Corporate/Tax tabs |
| CM | CM01 Bank Account, CM02 Statement Import (sample), CM03 Reconcile | source file fingerprint, unmatched/partial matches, balanced totals |
| SLA/GL | G01 SLA Events/Exceptions, G02 Rule Versions, G03 Journal, G04 Recurring/Allocation, G05 Reversal | Preview Dr/Cr, rule version, source chain, balance checks |
| Close | CL01 Close Cockpit, CL02 Adjustment, CL03 Year Close/Opening, CL04 TB | module checklist, pending items, close/reopen permissions, snapshot/version |
| Statements | FS01 Account Mapping, FS02 Financial Pack, FS03 Review/Finalize | TB, BS, P&L, Cash Flow, Equity, comparative period, manual schedules |

## ทิศทางภาพ

White/soft-gray background, dark-green navigation, primary green actions; amber hold/pending; red exception; monospaced amounts/codes ปรับเป็น Tailwind design tokens ภายหลัง SVG ใช้กราฟ/flow/icon ที่มี text equivalents ไม่ใช้ภาพแทนข้อมูลตาราง

## Wireframes สำหรับตรวจ

ภาพร่าง 6 หน้าใน `wireframes/` เป็น representative layouts ไม่ใช่หน้าจอครบทุก Screen ID แต่รายละเอียดรายการข้างต้นครอบคลุมฟอร์มและ workbench ที่ต้องออกแบบต่อ ตัวเลขทั้งหมด illustrative; ปุ่มเป็น annotation แบบนิ่ง ไม่ทำงาน

1. **Overview:** เมนูครบ, company/OU, สองวงจรงานและ close checklist
2. **PO & Budget:** PO header/lines, PR source, งบ, รับของ/match และ activity side panel
3. **Sales/Shipping/AR:** document chain, quantities, credit, open invoice และ collection promise
4. **Inventory:** stock/value counters, warehouse table, count variances และ policy ที่ยืนยันแล้ว
5. **FA:** source AP/CIP, asset detail, book comparison, depreciation preview
6. **Closing/TB:** company-specific close checklist, TB and statement pack, SLA exceptions

ลิงก์ Preview SVG และ PNG อยู่ใน README ปุ่ม Finalize/Approve/Adjust ในภาพเป็นเพียงภาพร่าง กฎ P01–P12 ได้รับยืนยันใน decision register แล้ว
