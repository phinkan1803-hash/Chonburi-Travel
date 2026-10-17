# ชลบุรีมีดี — Chonburi Travel
โครงงานเว็บไซต์แนะนำและประชาสัมพันธ์สถานที่ท่องเที่ยวในจังหวัดชลบุรี

## ความสามารถ
- หน้าแรกและการแสดงผลบนมือถือ
- ข้อมูลตั้งต้น 8 สถานที่ พร้อมประวัติ จุดเด่น ที่ตั้ง และลิงก์ Google Maps
- ค้นหาชื่อ ประเภท อำเภอ และกรองหมวดหมู่
- บันทึกสถานที่โปรดในเบราว์เซอร์ของผู้ชม
- หน้าผู้ดูแล เพิ่ม แก้ไข ลบ และเข้าสู่ระบบด้วย PHP + MySQL
- ใช้ภาพวาด CSS สื่อบรรยากาศ ภาพเหล่านี้ไม่ใช่ภาพถ่ายสถานที่จริง ผู้ดูแลเพิ่ม URL ภาพที่มีสิทธิ์ใช้งานได้

## แบบที่ 1: GitHub Pages (เริ่มต้นได้ทันที)
เป็นเว็บ HTML/CSS/JavaScript อ่านข้อมูลจาก assets/places.json ไม่มีระบบจัดการข้อมูลออนไลน์หรือ MySQL ในโหมดนี้
1. แตกไฟล์ ZIP ในคอมพิวเตอร์
2. เปิด repository ใน GitHub เลือก Add file → Upload files (repository ว่างให้กด uploading an existing file)
3. ลากไฟล์และโฟลเดอร์ **ภายใน** chonburi-travel ขึ้นไป อย่าอัปโหลด ZIP และอย่าให้ index.html ซ้อนในโฟลเดอร์อีกชั้น
4. กด Commit changes
5. ไปที่ Settings → Pages → Build and deployment → Source เลือก Deploy from a branch
6. Branch เลือก main และ /(root) แล้วกด Save
7. รอ GitHub สร้างเว็บ แล้วเปิด URL ที่ปรากฏในหน้า Pages โดยทั่วไปเป็น https://USERNAME.github.io/REPOSITORY/
8. แก้ไขข้อมูลโดยเปิด assets/places.json ใน GitHub กดแก้ไข แล้ว Commit changes

หากเลือกอัปโหลดด้วยเบราว์เซอร์และมองไม่เห็นไฟล์ .nojekyll ไม่เป็นไร เว็บนี้ไม่มีไฟล์ที่ต้องพึ่งการปิด Jekyll ส่วน .gitignore ต้องเพิ่มเองหากจะใช้ Git ในภายหลัง

## ทดลองในเครื่อง
เปิด Terminal ที่โฟลเดอร์นี้ แล้วใช้ `python -m http.server 8000` จากนั้นเปิด http://localhost:8000
อย่าดับเบิลคลิก index.html เพราะเบราว์เซอร์อาจไม่อนุญาต fetch ข้อมูล JSON ผ่าน file://

## แบบที่ 2: ระบบครบตามโครงงาน PHP + MySQL
ใช้ XAMPP หรือโฮสติ้งที่รองรับ PHP 8.1+ พร้อม pdo_mysql และ mbstring, MySQL 8+ (หรือ MariaDB ที่เข้ากันได้)
1. บน XAMPP วางโฟลเดอร์ใน htdocs/chonburi-travel และเปิด Apache กับ MySQL
2. เปิด phpMyAdmin แล้วนำเข้า database/schema.sql **ครั้งเดียว** เพื่อสร้างฐานข้อมูลและข้อมูลตั้งต้น
3. คัดลอก api/config.example.php เป็น api/config.php ตั้งค่า DSN ผู้ใช้และรหัสผ่านฐานข้อมูลจริง
4. สร้างแฮชรหัสผ่านด้วย PHP CLI: `php -r 'echo password_hash(readline("Password: "), PASSWORD_DEFAULT), PHP_EOL;'` แล้วใส่แฮชใน admin_password_hash ตั้ง admin_user ตามต้องการ
5. แก้ config.js ให้เป็น `window.TRAVEL_API = 'api/index.php';`
6. เปิด http://localhost/chonburi-travel/ และ admin.html เพื่อเข้าสู่ระบบ
7. เพิ่ม แก้ไข ลบ ข้อมูลแล้วกลับหน้าผู้ชมและรีเฟรช ข้อมูลจะมาจาก MySQL จริง
8. หากเผยแพร่ระบบเต็ม ใช้ HTTPS และโฮสติ้ง PHP/MySQL ตั้งค่าบัญชีฐานข้อมูลให้มีเฉพาะ SELECT, INSERT, UPDATE, DELETE บนฐานนี้ ไม่ใช้ root ในระบบจริง

GitHub ใช้เก็บโค้ดได้ทั้งสองแบบ แต่ GitHub Pages ไม่ประมวลผล PHP หรือ MySQL จึงไม่ควรเปลี่ยน config.js เป็นโหมด API บน GitHub Pages การแก้ข้อมูลใน MySQL ไม่เปลี่ยนไฟล์ JSON บน GitHub โดยอัตโนมัติ
ห้ามอัปโหลด api/config.php หรือรหัสผ่านจริงสู่ GitHub: .gitignore ช่วยเมื่อใช้ Git แต่การลากไฟล์อัปโหลดด้วยเว็บต้องตรวจและเว้นไฟล์นี้เอง ควรตั้ง rate limit การเข้าสู่ระบบตาม IP บนโฮสติ้งจริงเพิ่มเติม

## โครงสร้างไฟล์
index.html — หน้าผู้ชม
admin.html — หน้าจัดการข้อมูล
config.js — เลือกแหล่งข้อมูล JSON หรือ PHP API
assets/style.css — รูปแบบและภาพวาดบรรยากาศ
assets/app.js — ค้นหา กรอง ดูรายละเอียด และบันทึกที่โปรด
assets/admin.js — ส่วนติดต่อผู้ดูแล
assets/places.json — ข้อมูลสำหรับ GitHub Pages
api/index.php — เข้าสู่ระบบ ตรวจสิทธิ์ CSRF และ CRUD ด้วย prepared statements
api/config.example.php — ตัวอย่างการตั้งค่าที่ไม่มีรหัสผ่านจริง
database/schema.sql — โครงสร้าง MySQL และข้อมูลตั้งต้น

## ข้อมูลและภาพ
ข้อมูลเบื้องต้นเรียบเรียงจากการท่องเที่ยวแห่งประเทศไทย:
https://thai.tourismthailand.org/Destinations/Provinces/chon-buri/464
https://thai.tourismthailand.org/Attraction/เกาะสีชัง
ตรวจสอบ 17 ตุลาคม 2569 ไม่กำหนดเวลาเปิดหรือราคาโดยคาดเดา ผู้ใช้ควรตรวจสอบกับสถานที่ก่อนเดินทาง
ภาพตั้งต้นเป็นภาพวาดด้วย CSS ไม่ใช่ภาพจริง หากแทนภาพด้วย URL ต้องเป็น HTTPS และเป็นภาพของสถานที่นั้นที่ได้รับอนุญาตให้ใช้ ภาพภายนอกอาจไม่แสดงเมื่อเจ้าของ URL เปลี่ยนหรือปิดการเข้าถึง

## ขอบเขตการตรวจสอบ
ตรวจไวยากรณ์ JavaScript ความสอดคล้องข้อมูลตั้งต้น และทดสอบตรรกะค้นหา รายการโปรด การเปิดรายละเอียด/แผนที่ รวมถึงการป้องกัน HTML และ URL ที่ไม่ปลอดภัยด้วย Node.js แล้ว (เป็นการทดสอบจำลอง DOM) ต้องทดลองหน้าเว็บบนเบราว์เซอร์และทดสอบ PHP/MySQL บน XAMPP หรือโฮสติ้งจริงก่อนส่งงาน เนื่องจากสภาพแวดล้อมที่สร้างไฟล์นี้ไม่มี PHP/MySQL และยังไม่ได้เผยแพร่เข้าบัญชี GitHub ของผู้จัดทำ
