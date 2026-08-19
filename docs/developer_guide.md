# คู่มือการพัฒนาระบบ (Developer Guide)
**ระบบนิเทศภายใน (e-Supervisor) - โรงเรียนนราศึกษาธิการ**

---

## 1. การเตรียมสภาพแวดล้อม (Environment Setup)
เนื่องจากระบบใช้ Google Apps Script เป็น Backend ดังนั้นการพัฒนา (Development) จะต้องทำผ่าน Google Account

### สิ่งที่ต้องเตรียม:
1. บัญชี Google (แนะนำเป็นบัญชี Google Workspace ของโรงเรียน)
2. ติดตั้ง [Node.js](https://nodejs.org/) เพื่อใช้ NPM
3. ติดตั้ง Clasp (Command Line Apps Script Projects) โดยรันคำสั่ง:
   ```bash
   npm install -g @google/clasp
   ```
4. ล็อกอิน Clasp ด้วยคำสั่ง:
   ```bash
   clasp login
   ```

## 2. การติดตั้งโค้ด (Deployment)
1. ในโฟลเดอร์โปรเจกต์จะมีโฟลเดอร์ `apps-script/`
2. สร้างโปรเจกต์ Google Apps Script ใหม่ แล้วนำ `Script ID` มาใส่ในไฟล์ `.clasp.json` (หรือรัน `clasp create`)
3. พุชโค้ดขึ้น Google:
   ```bash
   cd apps-script
   clasp push
   ```
4. เข้าไปที่หน้าต่างของ Google Apps Script Editor
5. กดปุ่ม **"Deploy" > "New Deployment"**
6. เลือกประเภทเป็น **"Web App"**
   * **Execute as:** Me (ตัวคุณเอง)
   * **Who has access:** Anyone (เพื่อให้ Frontend เรียก API ได้)
7. กด **"Deploy"** และคัดลอก **Web App URL** มาเก็บไว้
8. นำ Web App URL ที่ได้ไปวางในไฟล์ Frontend ที่ `js/config.js`:
   ```javascript
   const API_URL = "YOUR_WEB_APP_URL_HERE";
   ```

## 3. สถาปัตยกรรม Backend (Google Apps Script)
โค้ด Backend อยู่ในไฟล์ `apps-script/Code.gs`

### 3.1 การจัดการ HTTP Request
ระบบใช้ `doGet(e)` และ `doPost(e)` สำหรับรับ Request
* **`doGet(e)`**: รับผิดชอบการ Render หน้า HTML ฝั่ง Frontend
* **`doPost(e)`**: ทำหน้าที่เป็น REST API (รับ JSON เข้ามาและตอบกลับเป็น JSON) โดยมีการทำ Routing ผ่าน `action` parameter เช่น:
  ```javascript
  switch (action) {
    case 'login': return jsonResponse(login(data));
    case 'createBooking': return jsonResponse(createBooking(data));
    // ...
  }
  ```

### 3.2 การตั้งค่าตัวแปรระบบ (Constants)
เปิดไฟล์ `Code.gs` และตรวจสอบการตั้งค่าเหล่านี้ให้ตรงกับของโรงเรียน:
```javascript
const SPREADSHEET_ID = 'YOUR_GOOGLE_SHEET_ID'; // ID ของ Google Sheet ที่ใช้เป็นฐานข้อมูล
const DRIVE_FOLDER_ID = 'YOUR_GOOGLE_DRIVE_FOLDER_ID'; // ID โฟลเดอร์สำหรับเก็บไฟล์งาน
const ADMIN_PASSWORD = 'admin123'; // รหัสผ่านตั้งต้นสำหรับ Admin
```

### 3.3 การเข้าถึงฐานข้อมูล (Google Sheets Database)
ระบบใช้ Google Sheets เป็น Database ผ่านฟังก์ชัน `getSheet(name)`
เมื่อเรียกใช้งาน ระบบจะเช็คว่ามี Sheet (ตาราง) นั้นอยู่หรือไม่ หากไม่มีจะสร้างขึ้นมาอัตโนมัติพร้อมกำหนดหัวคอลัมน์ (Headers) ให้ด้วยฟังก์ชัน `initializeSheet()`

## 4. โครงสร้าง Frontend
ไฟล์ HTML, CSS, JS จะแยกออกจากกัน (Separation of Concerns)

* **UI Components:** ใช้ Vanilla CSS ล้วน ไม่พึ่งพา Framework (อยู่ใน `css/style.css`) 
* **State Management:** ใช้ `APP.user` ในการเก็บข้อมูลผู้ใช้หลังล็อกอิน และเก็บ Token ลง `localStorage` เพื่อทำ Auto-login
* **API Calls:** อยู่ในฟังก์ชัน `apiPost(action, payload)` ของไฟล์ `js/app.js` ซึ่งจะดึง API_URL จาก `config.js` มาใช้เรียก

## 5. การปรับปรุงและการ Maintenance (How to modify)
* **หากต้องการเพิ่มฟิลด์ในฟอร์มการจอง:**
  1. เพิ่ม Input ใน `pages/booking.html`
  2. แก้ไข `js/app.js` ในฟังก์ชันที่ทำการบันทึกเพื่อแนบค่าฟิลด์ใหม่ไปกับ JSON Payload
  3. แก้ไข `Code.gs` ฟังก์ชัน `createBooking(data)` และเพิ่มคอลัมน์ใน Sheet `Booking` (แก้ไขอาร์เรย์ Header ใน `initializeSheet()`)
* **หากต้องการปรับโทนสีหน้าเว็บ:**
  1. เปิดไฟล์ `css/style.css`
  2. ค้นหา `:root` ซึ่งเป็นส่วนที่ประกาศ CSS Variables เอาไว้ (เช่น `--primary-color`, `--secondary-color`) แล้วเปลี่ยนโค้ดสีได้ทันที

## 6. ข้อควรระวัง (Security & Limits)
* **Google Quotas:** Google มีโควต้าการอ่าน/เขียน Sheets และ Drive (เช่น ส่งอีเมลได้จำกัดต่อวัน, อ่านเขียน Script ได้จำกัดเวลา) หากผู้ใช้เยอะมากๆ ควรพิจารณาทำ Caching 
* **Admin Role:** ผู้ที่มี Role = `admin` จะมองเห็นปุ่ม/เมนูพิเศษ (ถูกซ่อนด้วย CSS Class `.admin-only`) 
* **CORS (Cross-Origin Resource Sharing):** หากทดสอบ Frontend บน Localhost (`file://` หรือ `http://localhost`) จะสามารถเรียก API ของ GAS ได้ตามปกติหากทำตามขั้นตอนข้อ 2 (Who has access: Anyone) อย่างถูกต้อง
