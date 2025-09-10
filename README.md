# เครื่องคำนวณดอกเบี้ยทบต้นและ DCA

เว็บแอปเล็ก ๆ สำหรับคำนวณ Future Value (FV), เงินต้นสะสม และผลตอบแทนจากการลงทุนแบบ DCA พร้อมกราฟและตารางที่คัดลอกไป Google Sheets ได้ทันที

## Table of Contents
- [Head](#head)
- [body](#body)
    - [InputFormSection](#InputFormSection)
- [ตัวอย่าง](#ตัวอย่าง)
- [การมีส่วนร่วม](#การมีส่วนร่วม)
- [License](#license)

---

# `<head>`
```html
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>เครื่องคำนวณดอกเบี้ยทบต้นและ DCA</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Kanit', sans-serif;
        }
        .kanit-semibold {
            font-weight: 600;
        }
    </style>
</head>
```
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
```html
<body class="bg-gray-100 dark:bg-gray-900 text-gray-800 dark:text-gray-200 p-4 sm:p-6 lg:p-8">
    <div class="max-w-7xl mx-auto">
        
        <header class="text-center mb-8">
            <h1 class="text-3xl sm:text-4xl font-bold text-indigo-600 dark:text-indigo-400 kanit-semibold">เครื่องคำนวณดอกเบี้ยทบต้นและ DCA</h1>
            <p class="mt-2 text-lg text-gray-600 dark:text-gray-400">วางแผนอนาคตทางการเงินของคุณด้วยการคำนวณผลตอบแทนจากการลงทุน</p>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
```
## `<body class="...">`
 - `bg-gray-100 dark:bg-gray-900` → กำหนดสีพื้นหลัง เทาอ่อน เมื่อโหมดปกติ และ เทาเข้ม เมื่อโหมด Dark
 - `text-gray-800 dark:text-gray-200` → กำหนดสีข้อความเป็น เทาเข้ม (อ่านง่ายบนพื้นอ่อน) และ เทาอ่อน (อ่านง่ายบนพื้นมืด)
 - `p-4 sm:p-6 lg:p-8` → กำหนด padding รอบ ๆ เนื้อหา: 1rem (16px) บนมือถือ, 1.5rem บนหน้าจอขนาด sm ขึ้นไป, 2rem บนหน้าจอขนาด lg ขึ้นไป
สรุป: บรรยากาศของหน้าจะ responsive ทั้ง padding และสี โดยสลับตามโหมดสว่าง/มืด

## `<div class="max-w-7xl mx-auto">`
 - `max-w-7xl` → จำกัดความกว้างสูงสุดของเนื้อหาที่ ~1280px (ตาม scale ของ Tailwind)
 - `mx-auto` → จัดให้กล่องนี้อยู่กึ่งกลางแนวนอน (margin-left/right auto)
สรุป: ทำให้เนื้อหามี ความกว้างจำกัดและจัดกึ่งกลาง บนหน้าจอ

## `<header class="text-center mb-8">`
 - `text-center` → จัดข้อความกึ่งกลาง
 - `mb-8` → กำหนด margin-bottom = 2rem เพื่อเว้นที่ว่างด้านล่าง

### ภายใน `<header>` :
 - `<h1 class="...">`
    - `text-3xl sm:text-4xl` → ขนาดตัวอักษรใหญ่ (3xl = ~30px) และจะขยายเป็น 4xl (~36px) เมื่อหน้าจอ sm ขึ้นไป
    - `font-bold` → ทำตัวหนา
    - `text-indigo-600 dark:text-indigo-400` → สีหัวเรื่องเป็น indigo เข้มในโหมดปกติ และ indigo อ่อนในโหมดมืด
    - `kanit-semibold` → ใช้น้ำหนักฟอนต์ 600 (จาก custom class ที่คุณกำหนดไว้ใน `<style>`)
 - `<p class="...">`
    - `mt-2` → เพิ่มระยะห่างด้านบนเล็กน้อย
    - `text-lg` → ขนาดข้อความใหญ่กว่าปกติ (~18px)
    - `text-gray-600 dark:text-gray-400` → สีเทาเข้มในโหมดปกติ และเทาอ่อนในโหมดมืด
สรุป: ส่วน header ใช้สำหรับ ชื่อแอป (หัวเรื่องใหญ่) และ คำบรรยายสั้น ๆ อยู่กึ่งกลางหน้า

## `<div class="grid grid-cols-1 lg:grid-cols-3 gap-8">`
 - `grid` → ใช้ CSS Grid Layout
 - `grid-cols-1` → เริ่มต้นแสดงเป็น 1 คอลัมน์ (มือถือ/จอเล็ก)
 - `lg:grid-cols-3` → เมื่อถึง breakpoint `lg` (~1024px ขึ้นไป) จะเปลี่ยนเป็น 3 คอลัมน์
 - `gap-8` → ระยะห่างระหว่างคอลัมน์/แถว = 2rem
   
สรุป: ส่วนนี้คือ คอนเทนเนอร์หลักของ layout แบ่งเป็น grid แบบ responsive:
 - มือถือ: เนื้อหาเรียงลง 1 คอลัมน์
 - จอใหญ่: จะแบ่งเป็น 3 คอลัมน์ (ซ้าย: ฟอร์ม, ขวา 2 คอลัมน์: ผลลัพธ์)

## InputFormSection
### กล่องหลัก
```html
<div class="lg:col-span-1 bg-white dark:bg-gray-800 p-6 rounded-2xl shadow-lg">
```
 - `lg:col-span-1` → เมื่ออยู่ใน grid ขนาดใหญ่ กล่องนี้จะกินพื้นที่ 1 คอลัมน์
 - `bg-white dark:bg-gray-800` → พื้นหลังสีขาว (โหมดปกติ) และเทาเข้ม (โหมดมืด)
 - `p-6` → มี padding รอบกล่อง 1.5rem
 - `rounded-2xl` → มุมโค้งมนพิเศษ (ใหญ่)
 - `shadow-lg` → เงาลอยเด่น

คือกล่อง ฟอร์มป้อนข้อมูลการลงทุน

### หัวข้อของกล่อง
```html
<h2 class="text-2xl font-bold mb-6 border-b pb-3 border-gray-200 dark:border-gray-700">
    ข้อมูลการลงทุน
</h2>
```
 - `text-2xl font-bold` → ตัวใหญ่ (2xl ≈ 24px) หนา
 - `mb-6` → มี margin ล่าง 1.5rem
 - `border-b pb-3` → เส้นขีดล่าง + padding ล่าง 0.75rem
 - `border-gray-200 dark:border-gray-700` → เส้นสีเทาอ่อนในโหมดปกติ / เทาเข้มในโหมดมืด

ทำหน้าที่เป็นหัวข้อของฟอร์ม

### ฟอร์ม `id="calculator-form"`
```html
<form id="calculator-form" class="space-y-4">
```
 - `id="calculator-form"` → ใช้เรียกใน JavaScript เพื่อประมวลผล
 - `space-y-4` → แต่ละ `<div>` ภายในฟอร์มมีระยะห่างแนวตั้ง 1rem

