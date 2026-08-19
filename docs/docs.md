# เอกสารอ้างอิงและคู่มือระบบ (System Documentation)
**ชื่อระบบ:** ระบบนิเทศภายใน (e-Supervisor)
**องค์กร:** โรงเรียนนราศึกษาธิการ

---

## 1. ภาพรวมของระบบ (System Overview)
ระบบนิเทศภายใน (e-Supervisor) เป็นเว็บแอปพลิเคชันที่พัฒนาขึ้นเพื่ออำนวยความสะดวกในการบริหารจัดการกระบวนการนิเทศการสอนภายในโรงเรียนนราศึกษาธิการ โดยเชื่อมโยงระหว่าง "ครูผู้รับการนิเทศ" และ "ผู้นิเทศ/ผู้บริหาร" ให้สามารถดำเนินการแบบออนไลน์ได้อย่างครบวงจร ตั้งแต่การจองคิว, การส่งแผนการสอน, การประเมินผล และการออกรายงานสถิติ

## 2. โครงสร้างไฟล์ในโปรเจกต์ (Project Structure)
```text
/
├── index.html        # หน้าหลัก (Dashboard)
├── apps-script/      # โฟลเดอร์เก็บโค้ด Backend
│   ├── Code.gs       # Logic หลักของ Google Apps Script
│   ├── .clasp.json   # คอนฟิกสำหรับการใช้ Clasp ดึงโค้ดจาก GAS
│   └── appsscript.json # Manifest file
├── css/              # โฟลเดอร์เก็บสไตล์ชีต
│   └── style.css     # สไตล์หลักของเว็บไซต์
├── js/               # โฟลเดอร์เก็บสคริปต์ Frontend
│   ├── app.js        # ฟังก์ชันหลักในการทำงานของหน้าเว็บ
│   └── config.js     # การตั้งค่าที่จำเป็น (เช่น API URL)
├── pages/            # โฟลเดอร์เก็บหน้าเว็บย่อย
│   ├── admin.html    # หน้าสำหรับผู้ดูแลระบบ
│   ├── booking.html  # หน้าสำหรับจองวันนิเทศ
│   ├── evaluation.html # หน้าสำหรับประเมินผลการนิเทศ
│   ├── files.html    # หน้าสำหรับส่งไฟล์งาน
│   └── reports.html  # หน้าสำหรับดูรายงาน
└── deploy.ps1        # สคริปต์สำหรับ Deploy ระบบ
```

## 3. สถาปัตยกรรมระบบ (System Architecture)
ระบบถูกออกแบบโดยใช้สถาปัตยกรรมแบบ Serverless ผ่านเทคโนโลยีของ Google Workspace:

*   **Frontend (ส่วนแสดงผล):** พัฒนาด้วย HTML, CSS (Vanilla), และ JavaScript (Vanilla) เชื่อมต่อ API ด้วยฟังก์ชัน `fetch`
*   **Backend (ส่วนประมวลผล):** พัฒนาด้วย Google Apps Script (ไฟล์ `Code.gs`) โดยเปิดให้บริการในรูปแบบ Web App ทำหน้าที่เป็น REST-like API
*   **Database (ฐานข้อมูล):** ใช้ Google Sheets เป็นฐานข้อมูลหลัก (`SPREADSHEET_ID: 1KHJVpe1w4gRPAckL_gbs0WrGEjnDJylwny0od7jvkQM`)
*   **File Storage (ระบบจัดเก็บไฟล์):** ใช้ Google Drive สำหรับเก็บไฟล์งาน/แผนการสอนที่ครูอัปโหลด (`DRIVE_FOLDER_ID: 1FDj-gDOWuvq8A_kugvPrBhPZ8X4pHVUo`)

## 4. โครงสร้างฐานข้อมูล (Google Sheets)
ระบบเก็บข้อมูลแยกเป็นชีต (Sheets) ดังนี้:

*   **Users:** เก็บข้อมูลผู้ใช้งาน (Username, Password, ชื่อ-สกุล, สิทธิ์, กลุ่มสาระ, สถานะ)
*   **Booking:** เก็บข้อมูลการจอง (วันที่, เวลา, ชื่อครู, กลุ่มสาระ, คาบเรียน, รายวิชา, ระดับชั้น, ห้อง, สถานะการจอง)
*   **Files:** เก็บประวัติการส่งไฟล์ (ชื่อครู, Booking ID, ประเภทไฟล์, URL, Drive ID, สถานะการตรวจ)
*   **Evaluation (Supervision):** เก็บผลการนิเทศ (วันที่, จุดเด่น, จุดที่ควรพัฒนา, ข้อเสนอแนะ, ระดับคุณภาพ, คะแนน)
*   **Inspections:** เก็บรายละเอียดการสังเกตการสอนเชิงลึก (เทคนิคที่ใช้, คะแนนรายข้อ ฯลฯ)

## 5. วิธีการ Deploy และทดสอบ
1. ใช้ `clasp` ในการ push/pull ไฟล์ในโฟลเดอร์ `apps-script` ไปยัง Google Apps Script
2. ทำการ Deploy เป็น Web App ในหน้าต่างของ Google Apps Script เพื่อให้ได้ Endpoint URL
3. นำ Endpoint URL มาตั้งค่าในไฟล์ `js/config.js` ของ Frontend
4. การรัน Frontend แบบ Local สามารถใช้สคริปต์ `deploy.ps1` หรือเปิดไฟล์ HTML ปกติได้เลย
