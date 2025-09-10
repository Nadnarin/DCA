## Table of Contents
- [head](#head)
- [การใช้งาน](#การใช้งาน)
- [ตัวอย่าง](#ตัวอย่าง)
- [การมีส่วนร่วม](#การมีส่วนร่วม)
- [License](#license)

# '<head>'
## !DOCTYPE html
  ประกาศมาตรฐานเอกสารเป็น HTML5 เพื่อให้เบราว์เซอร์ตีความฟีเจอร์/เลย์เอาต์ด้วยโหมดมาตรฐาน (standards mode) แทน quirks mode

## html lang="th"
  บอกภาษาเริ่มต้นทั้งเอกสารเป็น “ไทย”  ช่วยเครื่องมือช่วยอ่าน (screen reader) ออกเสียงถูกภาษา ช่วย SEO และการเลือกฟอนต์ fallback/ฮินต์การตัดคำ

## meta charset="UTF-8"
  กำหนด encoding เป็น UTF-8 เพื่อรองรับอักขระไทยและสัญลักษณ์สากลโดยไม่เพี้ยน

## meta name="viewport" content="width=device-width, initial-scale=1.0"
  กำหนด scaling บนมือถือ: ความกว้างเท่าหน้าจอจริงและซูมเริ่มต้น 1.0 ทำให้ Tailwind responsive class ทำงานได้ตรงคาด
  
## <title>เครื่องคำนวณดอกเบี้ยทบต้นและ DCA</title>
  ข้อความบนแท็บเบราว์เซอร์ ใช้โดยเสิร์ชเอนจินเป็น title หลักของหน้า

## <script src="https://cdn.tailwindcss.com"></script>
  โหลด Tailwind “ผ่าน CDN” แบบ runtime (JIT) เหมาะต้นแบบ/หน้าเดี่ยวเร็ว ๆ 
  โปรดทราบ: โปรดักชันมัก build ไฟล์ CSS ที่ purge แล้วเพื่อลดขนาดและคงที่

## <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  โหลด Chart.js เพื่อวาดกราฟใน <canvas> ใช้แบบ UMD ผ่าน <script> สะดวกสำหรับหน้า statics
ถ้าแอปใหญ่ แนะนำระบุเวอร์ชันแน่นอน (pin) เพื่อลดความเสี่ยง breaking change

## link preconnect สำหรับ Google Fonts link rel="ชื่อ" href=""
  สั่งเบราว์เซอร์ “เปิดการเชื่อมต่อไว้ก่อน” (DNS/TLS handshake) กับโดเมนฟอนต์เพื่อลดเวลารอโหลดฟอนต์ครั้งแรก
  crossorigin จำเป็นกับ fonts.gstatic.com เพราะไฟล์ฟอนต์เป็น cross-origin และอาจใช้ cache/shared connection

## โหลดฟอนต์ Kanit link href="" rel=""
  ระบุช่วงน้ำหนักที่ใช้จริงเพื่อลด payload display=swap ให้ fallback font แสดงก่อน ลด FOIT (Flash of Invisible Text)

## style
  - บังคับทั้งหน้าควรใช้ Kanit ก่อน และ fallback เป็น sans-serif
  - คลาส .kanit-semibold เป็น utility เล็ก ๆ สำหรับน้ำหนัก 600 หากไม่อยากพึ่ง Tailwind class ในบางที่
