# Scope & decision register

## ยืนยันแล้ว

| ID | ข้อตกลง |
| --- | --- |
| D01 | โปรเจกต์ใหม่ แยกจาก erp-app; ทดแทน Oracle เป็นระยะ |
| D02 | Svelte + TypeScript, Tailwind CSS, SVG; NestJS + PostgreSQL บน Docker |
| D03 | Localhost, บันทึกธุรกรรมจริงด้วยข้อมูลทดสอบ; ระบบภายนอกจำลอง ไม่มีการจ่ายธนาคาร/ส่ง E-Tax จริง |
| D04 | Modular Monolith, Service ภายใน, transaction ร่วม ไม่มี Outbox/Message Broker |
| D05 | ICC และ ITK แยก Ledger/OU; Inventory Org แบบ Logical แยกแบรนด์ ต้นทุน/งวดแยก Org |
| D06 | ขายเครดิตและ Standard PO ในประเทศ เงินบาท; ไทย/อังกฤษ Desktop/Tablet และอนุมัติบนมือถือ |
| D07 | สายอนุมัติตาม OU/วงเงิน, ผู้สร้างอนุมัติตนเองไม่ได้, แก้ข้อมูลสำคัญต้องอนุมัติใหม่ |
| D08 | PR อนุมัติแล้วจองงบ, โอนไป PO ไม่จองซ้ำ, ยกเลิก/ปิดยอดค้างคืนงบ, รุ่นแรก Absolute |
| D09 | เครดิตรวมหนี้คงค้างกับยอดคำสั่งขายที่จองเครดิต ตรวจซ้ำก่อนจัดสรร และ Hold ตามเงื่อนไข |
| D10 | ห้ามสต็อกติดลบ, จองก่อนหยิบ, ส่งบางส่วน, ต้นทุนถัวเฉลี่ยเคลื่อนที่แยก Org |
| D11 | FA รวม Corporate/Tax Book, CIP แยกหลายทรัพย์สิน, เส้นตรงคิดรายวันลงบัญชีรายงวด; Tax Book ไม่ Post GL ซ้ำ |
| D12 | เพิ่ม Inventory/Costing/ตรวจนับ, PO/Budget, Shipping, Invoice/AR/Collection เป็นโมดูลรุ่นแรก |
| D13 | SLA ตรวจย้อนต้นทาง, GL, ปรับปรุง/กลับรายการ/Recurring/Allocation, ปิดงวดและปิดปี |
| D14 | TB, งบฐานะการเงิน, กำไรขาดทุน, กระแสเงินสด, การเปลี่ยนแปลงส่วนของเจ้าของ แยก ICC/ITK; ไม่รวม Consolidation/Elimination |
| D15 | PDF/Excel ตัวอย่าง, Mapping และข้อมูลประกอบงบที่ผู้รับผิดชอบตรวจรับก่อนใช้จริง |

Collection ติดตามหนี้/วางบิล/นัดชำระแยกจาก Receipt ซึ่งบันทึกเงินรับและตัดหนี้ นัดชำระไม่เปลี่ยนยอดลูกหนี้

## ประเด็นเพิ่มเติม P01–P12 — ยืนยันแล้วทั้งหมด

ผู้ใช้ยืนยันทั้งหมดหลังตรวจชุด Detailed Design เมื่อ 21 ตุลาคม 2026 สถานะต่อไปนี้แทนรายการรอยืนยันเดิม ข้อที่เป็น master data หรือตัวเลขนโยบายให้เป็นการตั้งค่าของแต่ละบริษัท ไม่ถือว่าผู้ใช้ได้ให้เลขบัญชี อัตราภาษี หรือวงเงินจริงซึ่งยังไม่มีในบทสนทนา

| ID | ข้อตกลงที่ยืนยัน | แนวทางนำไปใช้ |
| --- | --- | --- |
| P01 (Q26–29) | Physical/Cycle Count, พักตำแหน่งระหว่างนับ, Blind Count, นับซ้ำตามเกณฑ์และอนุมัติผลต่าง | เกณฑ์จำนวน/มูลค่าตั้งค่าได้; ผู้บันทึกไม่อนุมัติผลต่างตนเอง |
| P02 (Q30) | รวมหลาย SO/Shipment เป็น Invoice เมื่อบริษัท/OU, Bill-to, currency, terms และเงื่อนไขออกบิลตรงกัน | รักษา source allocation รายบรรทัด ป้องกันออกบิลซ้ำ |
| P03 (Q31) | ออก Invoice หลัง Ship Confirm เป็นแนวทางหลัก; รองรับรอ POD ตาม Customer Site | ตั้ง billing trigger และ recognition mapping แยก ไม่ใช้กฎเดียวแทนทุก Site |
| P04 (Q32) | เจ้าหน้าที่บันทึก carrier/tracking/POD เอง | ไม่มี real carrier API ตาม D03 |
| P05 (Q34) | เปลี่ยนงบจองเป็น actual ตอน AP ผ่านบัญชี; แยกงบ stock/capital/expense | ห้ามหักซ้ำจาก receipt, AP และ GL; คืนงบ/ปรับ actual ตามเอกสารอ้างอิง |
| P06 | tolerance ราคา/จำนวน และฐานยอดก่อน/หลังภาษีเป็นนโยบายตั้งค่าต่อบริษัท | บังคับ configuration พร้อม test cases; ไม่ยกตัวอย่าง tolerance ใน PDF เป็นค่าจริงทุกบริษัท |
| P07 | Costing policy มี version สำหรับ returns, price variance, backdating และ in-transit | เลือก basis ผ่าน configuration ที่มีผลตามวัน; แยกส่วน inventory/COGS/variance; ไม่แก้ประวัติ posted เงียบ ๆ |
| P08 | Calendar, rounding, tax, COA, numbering, owner/วงเงิน และ valuation accounts เป็น master/configuration | เงิน 2 ตำแหน่ง quantity/unit cost 6 ตำแหน่งตามแบบ; ตัวเลข/รหัสจริงต้องนำเข้าหรือตั้งค่า ไม่สร้างข้อมูลจริงจากการเดา |
| P09 | เส้นตรงรายวัน กำหนด day-count basis, leap-year, start/stop และ mid-period events ตาม Asset Book | รองรับ 365/fiscal-day เป็น configuration; ต้องมีชุดทดสอบ boundary |
| P10 | งบกระแสเงินสดวิธี indirect พร้อมข้อมูลยอดเปรียบเทียบและตารางประกอบ Equity | versioned mapping/schedules; ยอด source อ้างกลับ GL/หลักฐานได้ |
| P11 | แยก ICC/ITK ตาม Company/Legal Entity/Primary Ledger และมีหลาย OU พร้อม scope grants | org relationships และ assignment เป็น master data; sandbox ไม่ใช่ข้อมูลโครงสร้างจริงที่นำเข้าแล้ว |
| P12 | SLA mapping แบบ versioned, แยก valued/nonfinancial/financial-only และมี close/reopen policy ควบคุม | mapping และสิทธิ์เป็น configuration; ปิดถาวรไม่เปิดกลับ; reopen ปกติต้องมีสิทธิ์ เหตุผล Audit และทำ report revision |

ไม่มีประเด็น P01–P12 รอยืนยันแล้ว การตรวจค่าตั้งต้นและยอดนำเข้าระหว่าง UAT เป็นการทดสอบข้อมูล ไม่ใช่การขออนุมัติขอบเขตซ้ำ การยืนยันนี้ไม่เปลี่ยนสถานะชุดเอกสารเป็นโค้ดแอปที่พัฒนาแล้ว

## นอกการส่งมอบรุ่นแรก

Manufacturing, Blanket/Back-to-Back, Landed Cost/import, เงินตราต่างประเทศ/Revaluation, ช่องทาง Counter/Shop/Event/Van/Online แบบเต็ม, Consolidation, live bank/POS/E-Tax/WMS และรายงานที่ Blueprint ยังระบุ Confirm ทั้งนี้โครงสร้างรองรับการต่อยอด ไม่แสดงฟังก์ชันเหล่านี้ว่าใช้งานจริงแล้ว
