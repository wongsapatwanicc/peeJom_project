# REST API contract — design draft

ยังไม่ใช่ OpenAPI implementation. Prefix `/api/v1`; session auth และ role/scope validation ทุก endpoint.

## Common contract

- List: `?company_id=&ou_id=&status=&q=&page=&page_size=&sort=` ใช้ allowlisted sort, cap page_size 100 และคืน `{items,page,page_size,total}` สิทธิ์ filter ก่อน aggregate/export
- Mutations มี `Idempotency-Key` สำหรับ final commands และ `If-Match`/expected_version สำหรับแก้เอกสาร ปฏิเสธ stale document (409) ไม่ overwrite
- Body ระบุ business_date/gl_date ตามประเภทและ decimal string amounts/quantities; financial scope มาจาก document ที่ตรวจแล้ว ไม่ใช้ arbitrary company header แทนสิทธิ์
- Success command: `{id,number,status,version,links:{source,accounting,related}}`; final commit ยังไม่สำเร็จห้ามตอบ success
- Error: `{code,message,fields:[{path,rule}],correlation_id}`; 400 malformed, 401 unauthenticated, 403 scope/role, 404 missing scoped resource, 409 state/stale/duplicate-key payload conflict, 422 business validation
- Source IDs เป็น opaque strings; local UI ไม่เรียก direct database; print/export audit แยกจาก business post

## Resource and command catalogue

| Module | Resource / Commands |
| --- | --- |
| IAM/Org | `GET /me`, `/companies`, `/operating-units`, `/scopes`; session login/logout; approval inbox `/approvals`, `/{id}/approve`, `/return` |
| Master | `/customers`, `/customer-sites`, `/suppliers`, `/supplier-sites`, `/items`, `/uoms`, `/price-lists`, `/tax-codes` พร้อม draft/submit/approve ตามชนิด |
| Budget | `/budget-versions`, `/budget-lines`, `/budget-reservations`; `versions/{id}/submit|approve|activate`; `/budget-changes/{id}/approve`; reservation changes ผ่าน PR/PO service ไม่ให้ UI แก้ยอดตรง |
| PO | `/requisitions`, `/purchase-orders`; `/{id}/submit|approve|revise|cancel|close`; `POST /purchase-orders/from-requisition-lines` |
| Receiving/AP | `/receivings`, `/{id}/accept|put-away`; `/supplier-returns/{id}/post`; `/ap-invoices/{id}/match|post`; `/payment-proposals`; `/payments/{id}/finalize|reverse` |
| Inventory | `/stock-balances`, `/stock-movements`, `/reservations`, `/cost-history`, `/valuation`; `/transfers/{id}/dispatch|receive`; `/count-sessions/{id}/freeze|submit|recount|approve|adjust` (P01) |
| OM | `/quotations`, `/sales-orders`; `/{id}/submit|approve|allocate|cancel`; `/credit-holds/{id}/request-release|approve-release` |
| Shipping | `/deliveries`, `/picks`, `/shipments`; `shipments/{id}/confirm`; `/{id}/delivery-attempts`, `/{id}/pod`; `/billing-eligible-lines` (P02/P03) |
| Billing/AR | `/ar-invoices`, `/{id}/post`; `/sales-returns/{id}/receive`; `/credit-memos/{id}/post`; `/debit-memos/{id}/post`; `/receipts/{id}/finalize`; `/receipts/{id}/applications` และ `/applications/{id}/reverse` |
| Collection | `/collection-cases`, `/{id}/assign`, `/{id}/contacts`, `/{id}/promises`, `/{id}/disputes`; `/billing-batches`, `/customer-statements` |
| FA | `/capitalization-sources`, `/assets`, `/cip-projects`; `POST /capitalizations`; `/cip-projects/{id}/allocate`; `/depreciation-runs/{id}/preview|finalize`; `/asset-transfers/{id}/approve`; `/retirements/{id}/post` |
| CM | `/sample-bank-statements`, `/bank-statement-lines`; `/reconciliations/{id}/match|finalize|reverse` |
| SLA/GL | `/accounting-events`, `/{id}/preview|validate`; `/accounting-rules`; `/journals/{id}/submit|approve|post|reverse`; `/recurring-runs`, `/allocation-runs`, `/{id}/preview|post` |
| Close/Reports | `/module-periods/{id}/precheck|close`; `/gl-periods/{id}/precheck|close`; `/year-closes/{id}/preview|finalize`; `/reports/trial-balance`; `/financial-packs/{id}/generate|review|finalize`; `/report-runs/{id}/download` |

ขอบเขตชื่อ endpoint สื่อ intent; ไม่ใช้ generic `PATCH status=POSTED` ข้าม validation คำสั่งหลายรายการเป็น transaction ต่อ logical operation โดยระบุผล failed lines ไม่ silently partial commit

## Key request examples (contract data, not app code)

**Ship Confirm:** shipment_id, expected_version, actual_ship_date, lines[{shipment_line_id, quantity}], idempotency key. Server ตรวจ reserved/packed/open-period/location scope แล้วคืน movement/accounting references ไม่มี body field ที่สั่ง trusted debit/credit โดยตรง

**AR Invoice:** company/OU, bill-to-site, business/gl dates, lines[{shipment_line_id,billable_quantity}], terms/tax references; amount คำนวณฝั่ง serverจาก approved snapshots ไม่เชื่อ browser totals

**Receipt Application:** receipt_id, expected_version, applications[{open_item_id,amount}], application_date. Lock source unapplied money+all open items ตามลำดับ; amount เกิน residual → 422; create application/audit/SLA ใน transaction เดียว

**Count Adjustment:** approved_count_id, expected_version, posting_date, reason. ใช้ approved attempt/delta/cost policy จาก server ไม่รับ arbitrary adjustment qty จาก client P01 ยืนยันแล้ว เกณฑ์นับซ้ำและ approval limits อ่านจาก configuration

**TB:** company/ledger/year/from_period/to_period, account/segment filters, adjustment inclusion, stage; response rows[{account,opening_debit,opening_credit,period_debit,period_credit,closing_debit,closing_credit}], totals, cutoff, report_version. Total debit=credit เมื่อ full scoped TB; filtered subsets อาจไม่ balance จึงแสดง scope warning ตามจริง
