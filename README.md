# ICC ERP — Detailed Design & Wireframes

วันที่ 21 ตุลาคม 2026 · Revision 0.3 · สถานะ: **ยืนยันข้อกำหนดทั้งหมดแล้ว · ยังไม่เริ่มพัฒนา**

ชุดนี้เป็นเอกสารและภาพหน้าจอแบบนิ่ง ยังไม่มีโค้ดแอป ฐานข้อมูลใหม่ หรือการเปลี่ยนระบบ `../erp-app` ที่เปิดอยู่ ผู้ใช้ยืนยันประเด็น P01–P12 ทั้งหมดแล้ว ข้อกำหนดและค่าที่ตั้งค่าได้ระบุใน decision register การอัปเดตครั้งนี้เป็นงานเอกสาร ไม่ได้เริ่มเขียนโค้ดแอป

## เริ่มตรวจจากที่นี่

1. [ขอบเขตและรายการตัดสินใจ](01-scope-decisions.md)
2. [หน้าจอและ Wireframes](02-screens.md)
3. [สถาปัตยกรรมและแบบข้อมูล](03-architecture-data.md)
4. [Workflow และขอบเขต Transaction](04-workflows.md)
5. [API Contract ฉบับออกแบบ](05-api-contract.md)
6. [เกณฑ์ทดสอบและแผนส่งมอบ](06-validation-delivery.md)

คำศัพท์กลาง: [GLOSSARY](../GLOSSARY.md) · ข้อตัดสินใจสถาปัตยกรรม: [ADR-0001](../docs/adr/0001-local-modular-transactions.md) / [ADR-0002](../docs/adr/0002-separate-company-financials.md)

## Wireframes

ภาพทั้งหมดเป็นภาพร่างเพื่อทบทวนตำแหน่งข้อมูล/การทำงาน ตัวเลขและสถานะเป็นตัวอย่าง ไม่ใช่ข้อมูลบริษัทจริงหรือระบบที่ใช้งานได้แล้ว

| ภาพ | วัตถุประสงค์ |
| --- | --- |
| [01 Overview](wireframes/01-overview.svg) | เมนูครบทุกโมดูล KPI และงานรอดำเนินการ |
| [02 PO & Budget](wireframes/02-purchase-budget.svg) | PR/PO, วงเงิน, รับสินค้า และ Invoice Matching |
| [03 O2C](wireframes/03-sales-shipping-ar.svg) | Shipping, Invoice, AR และ Collection |
| [04 Inventory & Costing](wireframes/04-inventory-costing.svg) | คงเหลือ จอง ต้นทุน และรอบตรวจนับ |
| [05 FA](wireframes/05-fixed-assets.svg) | Corporate/Tax Book, CIP และค่าเสื่อม |
| [06 Closing & TB](wireframes/06-close-tb.svg) | ปิดโมดูล SLA/GL และงบแยกบริษัท |

## แหล่งอ้างอิง

- เอกสารหลัก: `C:\Users\99491\Downloads\ICC ERP Web Application – Architecture.pdf` (19 หน้า, Blueprint v0.1 Draft)
- ข้อสรุปในบทสนทนานี้มีลำดับสูงกว่าข้อเสนอใน PDF โดยเฉพาะ Svelte, การเรียก Service ภายใน, การเพิ่ม FA/INV/PO/Budget/Shipping/Collection และงบแยกบริษัท
- เอกสาร GL Phase 1 และโปรเจกต์เดิมเป็นบริบท ไม่ใช่ฐานโค้ดหรือ schema ที่นำมาใช้โดยอัตโนมัติ
- Blueprint ระบุเองว่า INV/MFG ยังไม่ได้ทำ Gap Analysis ครบ และบางรายงาน AR/PO รอยืนยัน รุ่นนี้จึงไม่ถือว่าครอบคลุม requirement ต้นฉบับทุกข้อ

จุดต่างจาก PDF: React → Svelte; Async cross-module/outbox → synchronous NestJS services ใน PostgreSQL transaction; งบรวม → แยก ICC/ITK เท่านั้น; ธนาคาร/POS/E-Tax จริง → จำลองในเครื่อง
