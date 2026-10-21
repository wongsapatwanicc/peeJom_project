# Design validation

วันที่ 21 ตุลาคม 2026

- จัดทำ scope/decision register, screen inventory, architecture/ERD, workflow/transaction boundaries, API contract และ acceptance/delivery documents
- อ่านยืนยัน Blueprint 19 หน้าและอ้าง page sections ที่เกี่ยวข้อง; ข้อตัดสินใจของผู้ใช้ override ส่วนที่ขัดกัน
- Wireframes SVG 6 ไฟล์ parse XML ผ่าน และ render เป็น PNG ได้ครบที่ขนาด 1440×1000
- ตรวจภาพ contact sheet ทั้ง 6 หน้า: ไม่พบข้อความซ้อน/กล่องทับ/ตารางล้นกรอบที่เห็นได้; layout ครอบคลุมทุก module ที่ผู้ใช้เพิ่ม
- ภาพเป็น mid-fidelity static wireframes ไม่ใช่ clickable prototype และไม่ได้ตรวจ interaction/mobile browser ของแอป
- ตัวเลขเป็นตัวอย่าง ส่วน TB แสดงเพียงบางบัญชี ไม่ใช่งบทดลองจริงที่ balance ด้วยชุดตัวอย่าง 3 บรรทัดนั้น
- ตรวจ links ภายในเอกสารและจำนวนไฟล์ตามรายการด้วย filesystem script
- ไม่มีการเขียนหรือแก้โค้ดแอปใน erp-app, ไม่รัน migration, ไม่เปลี่ยน Docker containers หรือฐานข้อมูลเดิม
- ไม่มี app integration test เพราะยังอยู่ design stage; test scenarios T01–T11 เป็นแผนทดสอบที่จะใช้เมื่อ implement

Revision 0.3: ผู้ใช้ยืนยัน P01–P12 ทั้งหมดอย่างชัดเจนแล้ว อัปเดต decision register, workflows, contracts และภาพร่างให้ตรงกัน ค่าตัวเลขและ master data ที่ไม่ได้ให้ยังเป็น configuration ไม่ใช่ข้อมูลจริงที่ผู้ใช้ระบุแล้ว
