# เครื่องคำนวณดอกเบี้ยทบต้นและ DCA

เว็บแอปเล็ก ๆ สำหรับคำนวณ Future Value (FV), เงินต้นสะสม และผลตอบแทนจากการลงทุนแบบ DCA พร้อมกราฟและตารางที่คัดลอกไป Google Sheets ได้ทันที

## Table of Contents
- [Head](#head)
- [body](#body)
    - [InputFormSection](#InputFormSection)
    - [ResultsSection](#ResultsSection)
    - [SummaryCards](#SummaryCards)
    - [Chart](#Chart)
    - [DataTable](#DataTable)
- [script](#script)
    - [กำหนดตัวแปรสำคัญ](#กำหนดตัวแปรสำคัญ)
    - [format](#format)
    - [submit](#submit)
    - [calculateAndDisplay](#calculateAndDisplay)

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
### `<!DOCTYPE html>`
ประกาศมาตรฐานเอกสารเป็น **HTML5** เพื่อให้เบราว์เซอร์ตีความฟีเจอร์/เลย์เอาต์ด้วยโหมดมาตรฐาน (standards mode)

### `<html lang="th">`
บอกภาษาเริ่มต้นทั้งเอกสารเป็น **ไทย**  
- ช่วย screen reader ออกเสียงถูกภาษา  
- ช่วย SEO และการเลือกฟอนต์ fallback/การตัดคำ

### `<meta charset="UTF-8">`
กำหนด encoding เป็น UTF-8 เพื่อรองรับอักขระไทย/สัญลักษณ์สากล

### `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
กำหนด scaling บนมือถือ: ความกว้างเท่าหน้าจอจริง, ซูมเริ่มต้น 1.0

### `<title>เครื่องคำนวณดอกเบี้ยทบต้นและ DCA</title>`
ข้อความบนแท็บเบราว์เซอร์ และใช้โดยเสิร์ชเอนจินเป็น title หลักของหน้า

### Tailwind CSS
```html
<script src="https://cdn.tailwindcss.com"></script>
```


### โหลด Chart.js เพื่อดูกราฟใน Canvas
สั่งเบราว์เซอร์ “เปิดการเชื่อมต่อไว้ก่อน” (DNS/TLS handshake) กับโดเมนฟอนต์เพื่อลดเวลารอโหลดฟอนต์ครั้งแรก
 - crossorigin จำเป็นกับ fonts.gstatic.com เพราะไฟล์ฟอนต์เป็น cross-origin และอาจใช้ cache/shared connection
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```

### โหลดฟอนต์ Kanit
```html
<link href="https://fonts.googleapis.com/css2?family=Kanit:wght@400;500;600;700&display=swap" rel="stylesheet">
```
 - ระบุช่วงน้ำหนักที่ใช้จริงเพื่อลด payload
 - display=swap ให้ fallback font แสดงก่อน ลด FOIT (Flash of Invisible Text)

### `<style>`
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
### `<body class="...">`
 - `bg-gray-100 dark:bg-gray-900` → กำหนดสีพื้นหลัง เทาอ่อน เมื่อโหมดปกติ และ เทาเข้ม เมื่อโหมด Dark
 - `text-gray-800 dark:text-gray-200` → กำหนดสีข้อความเป็น เทาเข้ม (อ่านง่ายบนพื้นอ่อน) และ เทาอ่อน (อ่านง่ายบนพื้นมืด)
 - `p-4 sm:p-6 lg:p-8` → กำหนด padding รอบ ๆ เนื้อหา: 1rem (16px) บนมือถือ, 1.5rem บนหน้าจอขนาด sm ขึ้นไป, 2rem บนหน้าจอขนาด lg ขึ้นไป
สรุป: บรรยากาศของหน้าจะ responsive ทั้ง padding และสี โดยสลับตามโหมดสว่าง/มืด

### `<div class="max-w-7xl mx-auto">`
 - `max-w-7xl` → จำกัดความกว้างสูงสุดของเนื้อหาที่ ~1280px (ตาม scale ของ Tailwind)
 - `mx-auto` → จัดให้กล่องนี้อยู่กึ่งกลางแนวนอน (margin-left/right auto)
สรุป: ทำให้เนื้อหามี ความกว้างจำกัดและจัดกึ่งกลาง บนหน้าจอ

### `<header class="text-center mb-8">`
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

### `<div class="grid grid-cols-1 lg:grid-cols-3 gap-8">`
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

### อินพุตที่ 1: เงินลงทุนเริ่มต้น (PV)
```html
<label for="initialInvestment">เงินลงทุนเริ่มต้น (PV)</label>
<div class="mt-1 relative rounded-md shadow-sm">
   <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
      <span class="text-gray-500 sm:text-sm">฿</span>
   </div>
   <input type="number" id="initialInvestment" value="100000" ... placeholder="0.00">
</div>
```
 - `<label>` → ข้อความกำกับฟิลด์นี้
 - `relative` + `absolute` → ทำให้สัญลักษณ์ ฿ อยู่ซ้ายในกล่องป้อนค่า
 - `pointer-events-none` → ป้องกันไม่ให้คลิกโดนสัญลักษณ์
 - `input type="number"` → ช่องป้อนตัวเลข, ค่าเริ่มต้น = 100000
 - `pl-7 pr-12` → padding ซ้ายเว้นที่ให้สัญลักษณ์, padding ขวาเว้นช่อง

### อินพุตที่ 2: เงินลงทุนเพิ่มต่อเดือน (DCA)
โครงสร้างเหมือน PV ต่างกันที่:
 - `id="monthlyContribution"`
 - ค่าเริ่มต้น `5000`
 - มีสัญลักษณ์ ฿ เหมือนกัน

### อินพุตที่ 3: อัตราผลตอบแทนต่อปี (i)
```html
<label for="interestRate">อัตราผลตอบแทนต่อปี (i)</label>
<div class="mt-1 relative rounded-md shadow-sm">
   <input type="number" id="interestRate" value="7" ... placeholder="0.00">
   <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3">
      <span class="text-gray-500 sm:text-sm">%</span>
   </div>
</div>
```
 - อินพุตชนิดตัวเลข ค่า default = 7
 - สัญลักษณ์ % อยู่ขอบขวาของกล่อง

### อินพุตที่ 4: ระยะเวลาลงทุน (n)
```html
<label for="years">ระยะเวลาลงทุน (n)</label>
<div class="mt-1 relative rounded-md shadow-sm">
   <input type="number" id="years" value="20" ... placeholder="0">
   <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3">
      <span class="text-gray-500 sm:text-sm">ปี</span>
   </div>
</div>
```
 - อินพุตชนิดตัวเลข ค่า default = 20
 - สัญลักษณ์ ปี อยู่ขอบขวา
 
### ปุ่มคำนวณ
```html
<button type="submit" class="w-full bg-indigo-600 text-white py-3 px-4 border border-transparent rounded-md shadow-sm text-base font-medium hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition-colors">
    คำนวณ
</button>
```
 - `w-full` → กว้างเต็ม container
 - `bg-indigo-600 hover:bg-indigo-700` → สีพื้นน้ำเงินม่วง + เปลี่ยนเฉดเมื่อ hover
 - `rounded-md shadow-sm` → มุมโค้งเล็ก + เงาบาง
 - `focus:ring-2` → มีเอฟเฟกต์วงแหวนเวลา focus
 - `transition-colors` → ทำให้สีเปลี่ยนแบบ smooth
   
เป็นปุ่มหลักของฟอร์ม เมื่อกดจะส่งข้อมูลไปให้ JavaScript คำนวณ


## ResultsSection
```html
<div id="results-container" class="lg:col-span-2 bg-white dark:bg-gray-800 p-6 rounded-2xl shadow-lg" style="display: none;">
    <h2 class="text-2xl font-bold mb-4 border-b pb-3 border-gray-200 dark:border-gray-700">ผลลัพธ์การลงทุน</h2>
```
- `id="results-container"` → ใช้สำหรับควบคุมการแสดงผลด้วย JavaScript (ตอนเริ่มต้น ซ่อนอยู่ ด้วย `display: none;`)
- `lg:col-span-2` → บนหน้าจอใหญ่ กล่องนี้จะกินพื้นที่ 2 คอลัมน์ ของ Grid
- `bg-white dark:bg-gray-800` → พื้นหลังขาว/เทาเข้มตามโหมด
- `p-6 rounded-2xl shadow-lg` → padding 1.5rem, มุมโค้งมน, และเงาลอย
- ข้อความตัวหนา ขนาด 2xl
- มีเส้นคั่นด้านล่าง (border-b) เพื่อแยกหัวข้อ

กล่องหลักที่เก็บผลลัพธ์การคำนวณทั้งหมด

## SummaryCards
```html
<div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6 text-center">
 <div class="bg-gray-100 dark:bg-gray-700 p-4 rounded-lg">
    <h3 class="text-sm font-medium text-gray-500 dark:text-gray-400">มูลค่าในอนาคต (FV)</h3>
    <p id="resultFV" class="text-2xl font-bold text-indigo-600 dark:text-indigo-400 mt-1">฿0</p>
 </div>
 <div class="bg-gray-100 dark:bg-gray-700 p-4 rounded-lg">
    <h3 class="text-sm font-medium text-gray-500 dark:text-gray-400">เงินต้นทั้งหมด</h3>
    <p id="resultPrincipal" class="text-2xl font-bold text-orange-500 mt-1">฿0</p>
 </div>
 <div class="bg-gray-100 dark:bg-gray-700 p-4 rounded-lg">
    <h3 class="text-sm font-medium text-gray-500 dark:text-gray-400">ดอกเบี้ย/ผลตอบแทน</h3>
    <p id="resultInterest" class="text-2xl font-bold text-yellow-500 mt-1">฿0</p>
 </div>
</div>
```
- `grid-cols-1` → บนจอเล็กเรียงการ์ดแนวตั้ง
- `md:grid-cols-3` → บนจอใหญ่เรียงเป็น 3 คอลัมน์
- `gap-4` → เว้นระยะห่างระหว่างการ์ด
- `text-center` → จัดเนื้อหาให้อยู่กึ่งกลาง

#### การ์ดแต่ละใบ:
 - มูลค่าในอนาคต (FV) → id `resultFV` แสดงมูลค่าลงทุนรวม ณ สิ้นงวด
   - ตัวเลขใหญ่ สี indig
 - เงินต้นทั้งหมด → id `resultPrincipal` แสดงผลรวมเงินลงทุนที่ใส่ไปเอง
    - ตัวเลขใหญ่ สีส้ม
 - ดอกเบี้ย/ผลตอบแทน → `id resultInterest` แสดงกำไรที่ได้เพิ่ม
    - ตัวเลขใหญ่ สีเหลือง

ใช้สีต่างกันเพื่อแยกมิติของข้อมูลได้ง่าย

## Chart
```html
<div class="mb-8">
    <canvas id="resultsChart"></canvas>
</div>
```
- `<canvas>` → Chart.js จะใช้พื้นที่นี้วาดกราฟแท่ง (Stacked Bar) เปรียบเทียบเงินต้น vs ผลตอบแทน
- `mb-8` → เว้นพื้นที่ด้านล่าง

## DataTable
```html
<div>
    <div class="flex justify-between items-center mb-4">
        <h3 class="text-xl font-bold">ตารางสรุปผลรายปี</h3>
        <button id="copy-button" class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-lg transition-colors text-sm">
            คัดลอกข้อมูลสำหรับ Google Sheets
        </button>
    </div>
    <div class="overflow-x-auto max-h-80 relative shadow-md rounded-lg">
        <table id="results-table" class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
             <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400 sticky top-0">
                <tr>
                    <th scope="col" class="py-3 px-6">ปีที่</th>
                    <th scope="col" class="py-3 px-6">เงินต้นสะสม</th>
                    <th scope="col" class="py-3 px-6">ดอกเบี้ย/ผลตอบแทนสะสม</th>
                    <th scope="col" class="py-3 px-6">มูลค่ารวมสิ้นปี</th>
                </tr>
            </thead>
            <tbody id="resultsTableBody">
                <!-- Data will be injected here by JavaScript -->
            </tbody>
        </table>
    </div>
    <p class="text-xs text-gray-500 mt-2">*การคำนวณนี้เป็นเพียงการประมาณการเพื่อประกอบการวางแผนเท่านั้น ผลตอบแทนจริงอาจแตกต่างกันไป</p>
</div>
```

### ส่วนหัวของตาราง + ปุ่มคัดลอก
```html
<div class="flex justify-between items-center mb-4">
   <h3 class="text-xl font-bold">ตารางสรุปผลรายปี</h3>
   <button id="copy-button" ...>คัดลอกข้อมูลสำหรับ Google Sheets</button>
</div>
```
- ใช้ Flexbox จัดหัวข้อไว้ด้านซ้าย และปุ่มไว้ด้านขวา
- ปุ่ม `#copy-button` จะเรียก JavaScript เพื่อคัดลอกข้อมูลในตารางไปยัง คลิปบอร์ด (พร้อมวางลง Google Sheets ได้ทันที)

### ตารางสรุปผล
```html
<div class="overflow-x-auto max-h-80 relative shadow-md rounded-lg">
   <table id="results-table" ...>
      <thead> ... </thead>
      <tbody id="resultsTableBody">
         <!-- JS เติมข้อมูลแต่ละปี -->
      </tbody>
   </table>
</div>
```
- `overflow-x-auto` → ถ้าตารางกว้างเกิน จะเลื่อนซ้าย-ขวาได้
- `max-h-80` → ความสูงสูงสุด ≈ 20rem ถ้ามีข้อมูลเยอะจะ scroll แนวตั้ง
- `shadow-md rounded-lg` → ใส่เงาและมุมโค้ง

`<thead>` → หัวตาราง (ติดด้านบน `sticky top-0`) มีคอลัมน์:
- ปีที่
- เงินต้นสะสม
- ดอกเบี้ย/ผลตอบแทนสะสม
- มูลค่ารวมสิ้นปี
`<tbody id="resultsTableBody">` → เนื้อหาตารางจะถูก inject ด้วย JavaScript (ผลการคำนวณแต่ละปี)

### หมายเหตุท้ายตาราง
```html
<p class="text-xs text-gray-500 mt-2">
   *การคำนวณนี้เป็นเพียงการประมาณการเพื่อประกอบการวางแผนเท่านั้น ผลตอบแทนจริงอาจแตกต่างกันไป
</p>
```
- ข้อความเล็ก สีเทาอ่อน → ทำหน้าที่เป็น Disclaimer แจ้งผู้ใช้ว่าเป็นเพียงการคาดการณ์


# script
`script` เป็น “ตัวควบคุมฝั่ง JavaScript” สำหรับเชื่อม ฟอร์ม (Input Section) เข้ากับ ผลลัพธ์ (Results Section)
```html
<script>
    document.addEventListener('DOMContentLoaded', () => {
        const form = document.getElementById('calculator-form');
        const resultsContainer = document.getElementById('results-container');
        let myChart;

        // Function to format currency
        const currencyFormatter = new Intl.NumberFormat('th-TH', {
            style: 'currency',
            currency: 'THB',
            minimumFractionDigits: 2,
            maximumFractionDigits: 2,
        });

        // Trigger calculation on page load with default values
        calculateAndDisplay();

        form.addEventListener('submit', (e) => {
            e.preventDefault();
            calculateAndDisplay();
        });
```

### `document.addEventListener('DOMContentLoaded', () => { ... });`
- เป็น Event Listener → จะรันฟังก์ชันด้านในก็ต่อเมื่อ DOM ของหน้าโหลดเสร็จแล้ว
- ป้องกันปัญหา JavaScript เรียก element ที่ยังไม่ถูกสร้างใน DOM

### กำหนดตัวแปรสำคัญ
```js
const form = document.getElementById('calculator-form');
const resultsContainer = document.getElementById('results-container');
let myChart;
```
- `form` → อ้างถึง `<form id="calculator-form">` (ช่องกรอกข้อมูล)
- `resultsContainer` → อ้างถึง `<div id="results-container">` (ผลลัพธ์ที่ซ่อนอยู่)
- `myChart` → ตัวแปรเก็บ instance ของกราฟ (จาก Chart.js) เพื่อ destroy/สร้างใหม่ได้ทุกครั้งที่คำนวณ

### format
ฟังก์ชัน format เงิน
```js
const currencyFormatter = new Intl.NumberFormat('th-TH', {
    style: 'currency',
    currency: 'THB',
    minimumFractionDigits: 2,
    maximumFractionDigits: 2,
});
```
- ใช้ `Intl.NumberFormat` เพื่อจัดรูปแบบตัวเลขเป็น สกุลเงินบาท (THB)
- บังคับให้มีทศนิยม 2 ตำแหน่งเสมอ เช่น
    - 100000 → `฿100,000.00`
    - 5000 → `฿5,000.00`
ทำให้ข้อมูลที่แสดงออกมาดูเป็นมาตรฐาน ไม่ใช่เลขดิบ

### คำนวณทันทีเมื่อโหลดหน้า
```js
calculateAndDisplay();
```
- เรียกฟังก์ชัน `calculateAndDisplay()` (ที่เขียนไว้ตอนท้ายสคริปต์) เพื่อให้เมื่อหน้าเพิ่งเปิดขึ้นมา จะแสดงผลจากค่า default (`PV=100000`, `DCA=5000`, `i=7%`,`n=20`) ทันที
- ช่วย UX → ผู้ใช้เห็นตัวอย่างผลลัพธ์โดยไม่ต้องกดปุ่มก่อน

### submit
จับ event "submit" ของฟอร์ม
```js
form.addEventListener('submit', (e) => {
    e.preventDefault();
    calculateAndDisplay();
});
```
- `form.addEventListener('submit', ...)` → รอจับเหตุการณ์เมื่อผู้ใช้กดปุ่ม คำนวณ หรือกด Enter ในฟอร์ม
- `e.preventDefault();` → ป้องกันพฤติกรรมปกติของฟอร์มที่จะ reload หน้า
- `calculateAndDisplay();` → เรียกฟังก์ชันคำนวณและอัปเดตผลใหม่ทันที

### calculateAndDisplay
ดึงค่าจากฟอร์ม
```js
function calculateAndDisplay() {
    const initialInvestment = parseFloat(document.getElementById('initialInvestment').value) || 0;
    const monthlyContribution = parseFloat(document.getElementById('monthlyContribution').value) || 0;
    const annualRate = parseFloat(document.getElementById('interestRate').value) / 100 || 0;
    const years = parseInt(document.getElementById('years').value) || 0;
    
    if (years === 0) return;
```
- `initialInvestment` → เงินลงทุนก้อนแรก (PV)
- `monthlyContribution` → เงินลงทุนเพิ่มทุกเดือน (DCA)
- `annualRate` → อัตราผลตอบแทนรายปี แปลง `%` → ทศนิยม เช่น 7 → 0.07
- `years` → ระยะเวลาลงทุน (ปี)
- ถ้าไม่ได้ระบุจำนวนปี → ยกเลิกการคำนวณทันที
ถ้าผู้ใช้ไม่ได้กรอก (ค่าเป็น `NaN` หรือว่าง) จะ fallback เป็น `0`

### เตรียมตัวแปรคำนวณ
```js
const monthlyRate = annualRate / 12;
const totalMonths = years * 12;

let futureValue = initialInvestment;
let totalPrincipal = initialInvestment;
let totalInterest = 0;

const annualData = [];
```
- `monthlyRate` → อัตราผลตอบแทนต่อเดือน
- `totalMonths` → จำนวนเดือนทั้งหมดที่ลงทุน
- `futureValue` → ค่าเงินรวม ณ ปัจจุบัน (เริ่มจากเงินต้นก้อนแรก)
- `totalPrincipal` → เงินต้นสะสม (รวมทั้งก้อนแรกและ DCA)
- `totalInterest` → ใช้เก็บดอกเบี้ยรวม (เริ่มที่ 0)
- `annualData` → Array เก็บผลลัพธ์รายปี (สำหรับตารางและกราฟ)

### วนลูปรายเดือน
```js
for (let m = 1; m <= totalMonths; m++) {
    const interestThisMonth = futureValue * monthlyRate;
    futureValue += interestThisMonth;
    futureValue += monthlyContribution;
    totalPrincipal += monthlyContribution;
```
สำหรับแต่ละเดือน `m` :
- คำนวณดอกเบี้ยเดือนนี้ = เงินรวมปัจจุบัน × อัตราต่อเดือน
- เพิ่มดอกเบี้ยเข้า `futureValue`
- เพิ่มเงิน DCA เข้า `futureValue`
- บวกเงิน DCA เข้า `totalPrincipal` (เงินที่เราใส่เอง)
ทำซ้ำทุกเดือนจนกว่าจะครบทั้งหมด

### เก็บข้อมูลสิ้นปี
```js
if (m % 12 === 0 || m === totalMonths) {
     const currentYear = Math.ceil(m / 12);
     const cumulativeInterest = futureValue - totalPrincipal;
     annualData[currentYear-1] = {
         year: currentYear,
         principal: totalPrincipal,
         interest: cumulativeInterest,
         total: futureValue
     };
}
```
- `m % 12 === 0` → เมื่อครบ 12 เดือน (สิ้นปี)
- `m === totalMonths` → หรือถ้าเป็นเดือนสุดท้ายของการลงทุน
จะทำการ snapshot ค่ามาเก็บไว้:
- `year` → ปีที่เท่าไร
- `principal` → เงินต้นสะสมจนถึงปีนั้น
- `interest` → ผลตอบแทนสะสม = มูลค่ารวม − เงินต้น
- `total` → มูลค่ารวมสิ้นปี (FV)
ข้อมูลนี้จะถูกนำไปใช้สร้าง ตารางรายปี และ กราฟ

### อัปเดตการ์ดสรุป (Summary Cards)
```js
document.getElementById('resultFV').textContent = currencyFormatter.format(futureValue);
document.getElementById('resultPrincipal').textContent = currencyFormatter.format(totalPrincipal);
totalInterest = futureValue - totalPrincipal;
document.getElementById('resultInterest').textContent = currencyFormatter.format(totalInterest);

```
- `resultFV` → ใส่มูลค่าในอนาคต (Future Value) ที่คำนวณได้
- `resultPrincipal` → ใส่เงินต้นสะสม (ที่ลงทุนไปทั้งหมด)
- `resultInterest` → คำนวณใหม่ = FV − เงินต้น แล้วใส่ในช่อง "ดอกเบี้ย/ผลตอบแทน"
ทุกค่าผ่าน `currencyFormatter.format(...)` เพื่อแสดงเป็นสกุลเงินบาท เช่น `฿1,234,567.89`

### อัปเดตตารางรายปี
```js
const tableBody = document.getElementById('resultsTableBody');
tableBody.innerHTML = '';
```
- เคลียร์เนื้อหาเดิมของ `<tbody>` ก่อน (กันไม่ให้ข้อมูลเก่าค้างอยู่)
```js
annualData.forEach(data => {
    const row = `
        <tr class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600">
            <th scope="row" class="py-4 px-6 font-medium text-gray-900 whitespace-nowrap dark:text-white">${data.year}</th>
            <td class="py-4 px-6">${currencyFormatter.format(data.principal)}</td>
            <td class="py-4 px-6">${currencyFormatter.format(data.interest)}</td>
            <td class="py-4 px-6 font-semibold">${currencyFormatter.format(data.total)}</td>
        </tr>
    `;
    tableBody.innerHTML += row;
});
```
- วนลูป `annualData` (ที่เก็บ snapshot สิ้นปีไว้)
- สำหรับแต่ละปี สร้าง `<tr>` ใส่ 4 ค่า:
    - ปี (`data.year`)
    - เงินต้นสะสม (`data.principal`)
    - ดอกเบี้ยสะสม (`data.interest`)
    - มูลค่ารวมสิ้นปี (`data.total`)
- ใช้ Tailwind classes ทำสีพื้น/โหมด dark/hover และจัด format ของ cell
ผลคือ ตารางจะสร้างใหม่ตามข้อมูลการคำนวณล่าสุด

### อัปเดตกราฟ
```js
updateChart(annualData);
```
- เรียกฟังก์ชัน `updateChart(...)` โดยส่ง `annualData` ไปวาดกราฟ Chart.js (stacked bar chart)
- กราฟจะแสดงเปรียบเทียบ เงินต้น vs ดอกเบี้ยสะสม ของแต่ละปี

### แสดงผลลัพธ์
```js
resultsContainer.style.display = 'block';
```
- เดิม `#results-container` ถูกซ่อนด้วย `style="display: none;"`
- บรรทัดนี้เปลี่ยนให้แสดงผลขึ้นมา → ผู้ใช้จะเห็นทั้งการ์ด, กราฟ, ตาราง
