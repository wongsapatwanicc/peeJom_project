# Traceability, acceptance & delivery

## Source traceability

| Requirement | Source / user decision | Screens | Acceptance |
| --- | --- | --- | --- |
| PO/Budget/Receiving/AP | Blueprint pp.6,8–9,13; D08,D12 | B01–04,P01–06,A01–05 | T01,T04,T08 |
| Inventory & Costing | pp.2,10–11; D05,D10,D12 | I01–09 | T01,T02,T03,T09 |
| OM/Shipping/Invoice | pp.9–10,13; D06,D12 | O01–04,F01–05,R01–03 | T02,T05,T08 |
| AR/Collection/Receipt | pp.10,13–15; user adds Collection | R04–05,C01–04 | T02,T06,T08 |
| FA | pp.12,14; D11 | FAI01–07 | T07,T09 |
| SLA/GL/Close | pp.3,6–8,14; user adds SLA/close/TB | G01–05,CL01–04,FS01–03 | T08–T11 |
| Company scope/SoD | pp.2,4,15; D05,D07,D14 | S01–05,H02 | T04,T08,T10 |
| No Outbox | User correction and ADR-0001 | All final command screens | T08 |

Blueprint เป็น synthesis ไม่ใช่ต้นฉบับ requirement ครบ 300 ข้อ ห้ามใช้ตารางนี้กล่าวว่า original requirements ทั้งหมดผ่านแล้ว

## End-to-end acceptance scenarios

ตัวเลขเป็นชุดข้อมูลสังเคราะห์เพื่อให้ตรวจซ้ำได้ ไม่ใช่ข้อมูลจริงหรือ tax policy จริง VAT/WHT/rounding ใช้ approved test configuration ก่อนเริ่ม UAT

| ID | Scenario | หลักฐานสำเร็จ |
| --- | --- | --- |
| T01 | P2P: PR 10 units → reserve → PO → receive 6+4 → AP match → partial payment → return 1 unit → supplier credit → settle net | budget ไม่ซ้ำ, stock 9 ก่อนขาย, receiving/AP qty correct, AP outstanding 0 หลัง net settlement, SLA balanced, GL/control reconcile |
| T02 | O2C: เริ่ม test stock แยก 10 → SO 10 → ship 6+4 → invoice → partial receipt → return 1 → credit → settle remaining | no negative stock, shipped/returned/billed quantity preserved, return disposition cost correct, AR residual0, credit exposure releases without gap/double count |
| T03 | Physical/Cycle Count: short/over/recount/deny/concurrent movement | attempt history, count policy P01 enforced, maker≠approver, adjustment once, stock value↔GL |
| T04 | Budget exact limit/over/cancel/amend/partial PO | atomic approval/reservation, release only unused commitment, actual policy P05 respected |
| T05 | Credit exact limit/overdue/hold/release/two simultaneous orders | approved exceptions audited, combined exposure not overspent |
| T06 | Collection promise/dispute/billing/actual receipt | promise ไม่ลด AR, partial application ไม่เกิน receipt/open balance, collection shows matched real receipt |
| T07 | AP capital → asset; CIP split → multiple assets; depreciation/transfer/count/retire | cost allocation conserved, no duplicate capitalization/run, Corporate posts GL, Tax does not duplicate, NBV/nonnegative residual, audit |
| T08 | double-click, two tabs, stale revision, missing account rule, scope attack | one operation per idempotency key, transaction rollback no orphan balance, role/scope rejects even direct API, no self approval |
| T09 | valuation/cost price variance, org transfer, inventory close, FA↔GL | qty/value reconcile, in-transit not doubled, issued-stock variance handled by policy, no backdated overwrite |
| T10 | AP/AR/CM/INV-COST/FA close → adjustments13/14 → GL close → year close | blockers visible, concurrent final posting cannot race closed period, one retained-earnings/opening batch per company/year |
| T11 | TB/Financial Pack company isolation, compare periods, finalize/revise | ICC never mixes ITK, opening+movement=closing, all posted ledger totals balanced, mapping/cutoff version saved, cashflow supplemental data tied, export totals match screen |

T01 และ T02 ใช้ independent/resettable fixtures ไม่ต่อยอดจำนวน10เดิมแบบที่ return ใน P2P แล้วเหลือ9แต่ยังสั่งส่ง10โดยไม่เติม stock ใน test

## Delivery plan & gates

| Stage | Deliverable | Gate |
| --- | --- | --- |
| Design (ปัจจุบัน) | 6 representative SVG wireframes, screen inventory, ERD, workflow, contracts, decisions/tests | P01–P12 ยืนยันแล้ว; ยังไม่ coding |
| Foundation | Org/IAM/Workflow/Master/Budget skeleton, minimal SLA/GL/Period/CM, Docker | maker/checker, scope, rounding/COA/calendar test config |
| Inventory Core | movement/reservation/cost/transfer | no negative, concurrency, valued/non-valued policy |
| P2P | PR→PO→Receiving→AP→Payment and budget | T01/T04/T08, ใช้ configuration P05–08 ที่ยืนยันแล้ว |
| FA | register/CIP/books/depreciation/transfer/count/retire | T07, ใช้ configuration P09/P12 ที่ยืนยันแล้ว |
| O2C | OM/Shipping/Invoice/AR/Collection/Receipt | T02/T05/T06, ใช้นโยบาย P02–04/P06 ที่ยืนยันแล้ว |
| Close & UAT | count, reconciliation, close/year, TB/statements/export | T03/T09–11, ใช้นโยบาย P01/P10–12 ที่ยืนยันแล้ว |

ไม่ประมาณวันส่งมอบ/throughput โดยไม่มีจำนวนผู้ใช้ ปริมาณธุรกรรม และทีมพัฒนาจริง ค่า NFR ใน PDF เป็นข้อเสนอ ไม่ถือเป็น SLA ที่ผู้ใช้รับรอง

## Design QA

ตรวจความครบ module/screen/source/test mapping, XML SVG parse, ขนาดและ render ภาพ, relative links และความสอดคล้องของ confirmed decisions ไม่มีการรัน integration tests ของแอปเพราะชุดนี้ยังไม่มี app code ผล visual QA แยกใน `design-validation.md`.
