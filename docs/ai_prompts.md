# AI System Prompt & Context Guidelines
**Project:** ระบบนิเทศภายใน (e-Supervisor) - โรงเรียนนราศึกษาธิการ

เอกสารฉบับนี้ใช้สำหรับเป็น **Prompt หรือ Context เริ่มต้น** เวลาที่คุณต้องการนำโปรเจกต์นี้ไปให้ AI (เช่น ChatGPT, Claude, Gemini) ช่วยพัฒนาต่อหรือแก้ไขบั๊ก เพื่อให้ AI เข้าใจโครงสร้างระบบทั้งหมดทันที

---

## 📋 คำสั่งเริ่มต้น (Copy ไปใช้กับ AI ได้เลย)

> **"ฉันต้องการพัฒนาและแก้ไขระบบนิเทศภายใน (e-Supervisor) ของโรงเรียน ต่อไปนี้คือข้อมูล Context ของระบบ โปรดอ่านและทำความเข้าใจก่อนเริ่มช่วยเหลือฉัน"**

### [Context Information]
**1. Overview:**
โปรเจกต์นี้คือเว็บแอปพลิเคชันสำหรับการนิเทศการสอนภายในโรงเรียน (e-Supervisor) มีฟีเจอร์หลักคือ การจองวันนิเทศของครู, การอัปโหลดแผนการสอน, การประเมินให้คะแนนของผู้นิเทศ และแดชบอร์ดดูรายงาน

**2. Technology Stack:**
- **Frontend:** HTML5, CSS (Vanilla), JavaScript (Vanilla) (เป็น Single Page App + Multi-page ผสมกัน)
- **Backend:** Google Apps Script (ไฟล์ `Code.gs`) ให้บริการเป็น REST-like API (รับ-ส่ง JSON ผ่าน `doPost(e)`)
- **Database:** Google Sheets
- **Storage:** Google Drive สำหรับเก็บไฟล์แนบ

**3. Project Structure:**
```text
/
├── index.html        # หน้าหลักและ Dashboard สถิติ
├── apps-script/      # โค้ด Backend
│   └── Code.gs       # Logic หลัก (การเชื่อมต่อ Sheet/Drive)
├── css/
│   └── style.css     # สไตล์หลัก
├── js/
│   ├── app.js        # ฟังก์ชัน Frontend ทั้งหมด, API calls (fetch)
│   └── config.js     # เก็บค่า URL ของ Google Web App
└── pages/            # หน้าอื่นๆ 
    ├── booking.html  
    ├── evaluation.html
    ├── files.html
    └── reports.html
```

**4. Database Schema (Google Sheets):**
- **Users:** Username, Password, Full Name, Role, Department, Active
- **Booking:** Timestamp, Date, Time, Teacher Name, Department, Period, Subject Name, Subject Code, Class Level, Room, Status, Notes
- **Files:** Timestamp, Teacher Name, Booking ID, File Type, File Name, File URL, Drive File ID, Status, Admin Note, Reviewed By, Reviewed Date
- **Supervision:** Timestamp, Teacher Name, Supervision Date, Booking ID, Strengths, Improvements, Suggestions, Quality Level, Score, Evaluated By, Booking Reference

**5. AI Guidelines (กฎสำหรับ AI เวลาแก้ไขโค้ด):**
1. **No Frameworks:** ห้ามใช้ Framework ใดๆ เพิ่มเติม (ห้ามใช้ React, Vue, Tailwind) ให้ใช้ Vanilla HTML/CSS/JS เท่านั้น
2. **Keep Design System:** ให้รักษาโครงสร้าง CSS เดิมไว้ หากต้องเพิ่ม UI component ใหม่ ให้ใช้ CSS class ที่มีอยู่แล้ว หรือเขียนต่อท้ายในสไตล์เดิม (เน้นความเรียบง่าย สะอาดตา)
3. **Google Apps Script Constraints:** เวลาแก้ไข Backend (`Code.gs`) ต้องคำนึงถึงข้อจำกัดโควต้าของ Google เสมอ ห้ามเขียนลูปที่อาจทำให้เกิด Timeout (6 นาที) 
4. **Localization:** การติดต่อผู้ใช้ (UI) และข้อความแจ้งเตือนทั้งหมด ต้องใช้ **ภาษาไทย**

---
*คำสั่งของคุณที่จะให้ AI ทำ: [พิมพ์สิ่งที่คุณต้องการแก้ไข หรือฟีเจอร์ใหม่ที่ต้องการเพิ่มตรงนี้]*
