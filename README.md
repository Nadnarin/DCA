# เครื่องคำนวณดอกเบี้ยทบต้นและ DCA

เว็บแอปเล็ก ๆ สำหรับคำนวณ Future Value (FV), เงินต้นสะสม และผลตอบแทนจากการลงทุนแบบ DCA พร้อมกราฟและตารางที่คัดลอกไป Google Sheets ได้ทันที

## Table of Contents
- [Head](#head)
- [การใช้งาน](#การใช้งาน)
- [ตัวอย่าง](#ตัวอย่าง)
- [การมีส่วนร่วม](#การมีส่วนร่วม)
- [License](#license)

---

# Head

## `<!DOCTYPE html>`
ประกาศมาตรฐานเอกสารเป็น **HTML5** เพื่อให้เบราว์เซอร์ตีความฟีเจอร์/เลย์เอาต์ด้วยโหมดมาตรฐาน (standards mode)

## `<html lang="th">`
บอกภาษาเริ่มต้นทั้งเอกสารเป็น **ไทย**  
- ช่วย screen reader ออกเสียงถูกภาษา  
- ช่วย SEO และการเลือกฟอนต์ fallback/การตัดคำ

## `<meta charset="UTF-8">`
กำหนด encoding เป็น UTF-8 เพื่อรองรับอักขระไทย/สัญลักษณ์สากล

## `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
กำหนด scaling บนมือถือ: ความกว้างเท่าหน้าจอจริง, ซูมเริ่มต้น 1.0

## `<title>เครื่องคำนวณดอกเบี้ยทบต้นและ DCA</title>`
ข้อความบนแท็บเบราว์เซอร์ และใช้โดยเสิร์ชเอนจินเป็น title หลักของหน้า

## Tailwind CSS
```html
<script src="https://cdn.tailwindcss.com"></script>
