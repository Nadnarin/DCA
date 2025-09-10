# เครื่องคำนวณดอกเบี้ยทบต้นและ DCA

เว็บแอปเล็ก ๆ สำหรับคำนวณ Future Value (FV), เงินต้นสะสม และผลตอบแทนจากการลงทุนแบบ DCA พร้อมกราฟและตารางที่คัดลอกไป Google Sheets ได้ทันที

## Table of Contents
- [Head](#head)
- [body](#body)
- [ตัวอย่าง](#ตัวอย่าง)
- [การมีส่วนร่วม](#การมีส่วนร่วม)
- [License](#license)

---

# `<head>`

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
```


## โหลด Chart.js เพื่อดูกราฟใน Canvas
สั่งเบราว์เซอร์ “เปิดการเชื่อมต่อไว้ก่อน” (DNS/TLS handshake) กับโดเมนฟอนต์เพื่อลดเวลารอโหลดฟอนต์ครั้งแรก
 - crossorigin จำเป็นกับ fonts.gstatic.com เพราะไฟล์ฟอนต์เป็น cross-origin และอาจใช้ cache/shared connection
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```

## โหลดฟอนต์ Kanit
```html
<link href="https://fonts.googleapis.com/css2?family=Kanit:wght@400;500;600;700&display=swap" rel="stylesheet">
```
 - ระบุช่วงน้ำหนักที่ใช้จริงเพื่อลด payload
 - display=swap ให้ fallback font แสดงก่อน ลด FOIT (Flash of Invisible Text)

## `<style>`
  - บังคับทั้งหน้าควรใช้ Kanit ก่อน และ fallback เป็น sans-serif
  - คลาส .kanit-semibold เป็น utility เล็ก ๆ สำหรับน้ำหนัก 600 หากไม่อยากพึ่ง Tailwind class ในบางที่

# `<body>`
## `<body class="...">`
 - `bg-gray-100 dark:bg-gray-900` → กำหนดสีพื้นหลัง เทาอ่อน เมื่อโหมดปกติ และ เทาเข้ม เมื่อโหมด Dark
 - `text-gray-800 dark:text-gray-200` → กำหนดสีข้อความเป็น เทาเข้ม (อ่านง่ายบนพื้นอ่อน) และ เทาอ่อน (อ่านง่ายบนพื้นมืด)
 - `p-4 sm:p-6 lg:p-8` → กำหนด padding รอบ ๆ เนื้อหา: 1rem (16px) บนมือถือ, 1.5rem บนหน้าจอขนาด sm ขึ้นไป, 2rem บนหน้าจอขนาด lg ขึ้นไป
สรุป: บรรยากาศของหน้าจะ responsive ทั้ง padding และสี โดยสลับตามโหมดสว่าง/มืด



