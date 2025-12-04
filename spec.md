# LightKid – Requirement & Functional Spec (MVP)

## 1. Overview

**ชื่อโปรเจกต์:** LightKid  
**ประเภท:** SaaS / Mobile-first (iOS + Android)  
**เป้าหมาย:**  
แอปสำหรับพ่อแม่ใช้ติดตามการใช้ชีวิตและพัฒนาการของลูก ตั้งแต่แรกเกิดจนโต เช่น

- แท็กกิจกรรมสำคัญ: กิน, นอน, ขับถ่าย, เล่น ฯลฯ
- ติดตามพัฒนาการแต่ละช่วงวัย
- เทียบเกณฑ์การเจริญเติบโต (เช่น น้ำหนัก-ส่วนสูงตามอายุ)
- วิเคราะห์การนอนและช่วยแนะนำเวลาเข้านอน/ตื่นนอนที่เหมาะสม

**แพลตฟอร์มหลัก (MVP):**

- Mobile App: Flutter (iOS + Android)
- Backend: REST API (Go Fiber v2)
- Database: PostgreSQL

---

## 2. User & Role

### 2.1 ประเภทผู้ใช้

- **User (Parent/Guardian)** – ผู้ปกครอง 1 คน ต่อ 1 บัญชี (ใน MVP)
- 1 User = 1 ครอบครัว
- 1 User สามารถสร้างเด็ก (Child) ได้หลายคน

### 2.2 Authentication

- ลงทะเบียนด้วย Email + Password
- Login / Logout
- ใช้ JWT (Access Token + Refresh Token)
- รองรับ Social Login (Google / Apple) ในเฟสถัดไป (นอก scope MVP)

---

## 3. MVP Feature Scope

### 3.1 ระบบ Login

**ฟีเจอร์:**

- Register ด้วย email + password
- Login ด้วย email + password
- Refresh token
- Logout / revoke refresh token (optional)

**Acceptance Criteria:**

- สมัครสมาชิกแล้ว Login ได้
- ถ้า login ผิด password จะได้ error ที่เหมาะสม
- Token ที่หมดอายุจะต้อง refresh ได้ (ผ่าน endpoint `/auth/refresh`)

---

### 3.2 ระบบ Tracking (กิจกรรม / การใช้งานจริง)

ตัวอย่างที่ต้องการ track:

- ขับถ่าย
- กินนม (เข้าเต้า / ขวด)
- การนอน
- เล่นกับพ่อแม่
- อื่นๆ ที่ผู้ใช้กำหนดเองได้

#### 3.2.1 สร้างประเภท Tracking เองได้

**ฟีเจอร์:**

- User สามารถสร้าง Tracker ของตัวเองได้
- Field เบื้องต้นของ Tracker:
  - ชื่อ (เช่น “เล่นกับพ่อแม่”)
  - Icon (เลือกจากชุด icon key)
  - สี (color hex)
  - หมวดหมู่ (feed / sleep / poop / play / custom / etc.)
  - หน่วย default (เช่น ml, oz, นาที, ชั่วโมง, count)

**Acceptance Criteria:**

- สร้าง tracker ใหม่แล้วสามารถใช้ในหน้าเพิ่ม log ได้
- แก้ไขชื่อ, icon, สี และ default unit ของ tracker ได้
- ลบ tracker แล้วต้องไม่หาย log เดิม (log ยังอยู่ แต่อาจ mark ว่า tracker ถูกลบ)

#### 3.2.2 บันทึก Log ของแต่ละกิจกรรม

**ต่อหนึ่ง Log ต้องมี:**

- เด็กที่เลือก (`child`)
- Tracker ที่เลือก (`tracker`)
- เวลา (`logged_at`)
- ค่าที่ต้องการบันทึก (`value`) – optional
- หน่วย (`unit`) – เลือกได้ เช่น ml, oz, นาที, count
- ค่า base (ใช้สำหรับ normalize เช่น ml, minute) – คำนวณฝั่ง Backend
- note (ข้อความกำกับสั้นๆ)

**Requirement ด้าน unit:**

- ต้องรองรับการบันทึกหน่วยต่างกัน (เช่น ml, oz)
- Backend ต้องเก็บค่า base (เช่น ml, minute) เพื่อให้สรุปรายงานได้ง่าย

**ตัวอย่าง:**

- กินนม 80 ml → `value = 80`, `unit = ml`, `base_value = 80`
- กินนม 3 oz → `value = 3`, `unit = oz`, `base_value = 88.72` (ถ้า 1 oz ≈ 29.5735 ml)

---

### 3.3 จัดการข้อมูลลูก (Children Management)

**ฟีเจอร์:**

- เพิ่มลูกใหม่
- แก้ไขข้อมูลลูก
- ลบลูก

**ข้อมูลต่อเด็ก 1 คน:**

- ชื่อ
- ชื่อเล่น (optional)
- วันเกิด (จำเป็น)
- เพศ (M / F / ไม่ระบุ)
- สีธีมของเด็ก (ใช้ใน UI)
- น้ำหนักแรกเกิด
- ส่วนสูงแรกเกิด
- note อื่นๆ

**Acceptance Criteria:**

- User สามารถเพิ่มลูกได้มากกว่า 1 คน
- สามารถสลับดูข้อมูลของแต่ละคนได้ใน Dashboard

---

### 3.4 Dashboard (Daily Timeline & Quick View)

**ฟีเจอร์:**

- เลือกเด็กที่ต้องการดู (child switcher)
- เลือกวันที่ (ค่า default = วันนี้)
- แสดง log ของวันนั้น เรียงตามเวลา
- โฟกัสช่วงเวลา 3 ชั่วโมงล่าสุด (เช่น now - 3h → now)
- สามารถกดเพิ่ม log ได้จากหน้า Dashboard โดยเลือก tracker ที่ใช้งานบ่อย

**ข้อมูลที่แสดงบน Dashboard:**

- Timeline กิจกรรม (กินนม, นอน, ขับถ่าย, เล่น ฯลฯ)
- สรุปสั้นๆ ของวัน (เช่น กินนมกี่ครั้ง ขับถ่ายกี่ครั้ง นอนกี่ชั่วโมง)

---

### 3.5 พัฒนาการ (Milestones)

**ฟีเจอร์:**

- แสดงรายการพัฒนาการมาตรฐาน (ระบบเตรียม catalog)
- User เลือกติ๊กได้ว่า “ทำได้แล้ว”
- บันทึกวันที่ทำได้ (achieved_at)
- เพิ่ม note ได้

**ตัวอย่าง Milestone:**

- ยิ้มตอบได้
- นั่งเองได้
- คลานได้
- เดินได้เอง
- เข้าห้องน้ำได้เอง
- มีฟัน X ซี่ เป็นต้น

**Acceptance Criteria:**

- ผู้ใช้สามารถดู checklist พัฒนาการของเด็กตามช่วงอายุได้
- สามารถ mark ว่าพัฒนาการใด “ทำได้แล้ว” พร้อมวันที่
- แสดงสรุปพัฒนาการทั้งหมดที่เด็กทำได้แล้ว

---

### 3.6 เทียบเกณฑ์การเติบโต (Growth Standard)

**ฟีเจอร์:**

- แสดงกราฟ:
  - น้ำหนัก vs อายุ
  - ส่วนสูง vs อายุ
- เทียบกับเกณฑ์มาตรฐานตามเพศและอายุ (percentile)

**Requirement:**

- ต้องมีตารางมาตรฐาน (`growth_standards`) สำหรับน้ำหนักและส่วนสูงตามอายุ (เดือน) แยกตามเพศ
- ฟีเจอร์นี้สามารถเริ่มจากการใช้ข้อมูล mock และค่อยเชื่อมข้อมูลจริงภายหลัง

**Acceptance Criteria:**

- ผู้ใช้สามารถเลือกเด็ก → ดูกราฟน้ำหนักและส่วนสูงของเด็กคนนั้นได้
- มีการแสดงว่าตอนนี้เด็กอยู่ช่วงประมาณ percentile ใด (เช่น ใกล้ p50 หรือ p85)

---

### 3.7 รายงานสรุป (Reports)

**ฟีเจอร์:**

- สรุป “วันนี้เด็กทำอะไรบ้าง”
- สรุป “เดือนนี้ / ปีนี้” แบบรวม

**ตัวอย่างข้อมูลในรายงาน:**

- กินนม:
  - จำนวนครั้ง
  - ปริมาณรวม
- ขับถ่าย:
  - จำนวนครั้ง
- การนอน:
  - จำนวนชั่วโมงรวม
  - เฉลี่ยต่อวัน
- พัฒนาการ:
  - Milestone ที่ทำได้ในช่วงเวลาที่เลือก

**ช่วงเวลา:**

- today
- this week
- this month
- this year
- custom range

---

### 3.8 การวิเคราะห์การนอน + คำแนะนำ (Sleep Analysis & Recommendation)

**เวอร์ชัน MVP: Rule-based (ไม่ต้อง AI หนักมาก):**

**Input:**

- Log การนอนของเด็กในช่วง 3–7 วัน (start sleep / wake up)
- อายุของเด็ก (เดือน)

**Output (ตัวอย่าง):**

- ช่วงเวลาที่แนะนำให้เริ่มพาลูกนอน (nap ต่อไป)
- จำนวนชั่วโมงที่ควรนอนต่อวัน (ตามอายุ)
- แจ้งเตือนเมื่อใกล้เวลานอน (ผ่าน push notification)

**Acceptance Criteria:**

- ระบบสามารถ generate ข้อความแนะนำเวลานอนได้จาก pattern การนอน + อายุ
- ผู้ใช้สามารถเปิด/ปิดการแจ้งเตือนเรื่องการนอนได้

---

## 4. Non-functional Requirements (เบื้องต้น)

- **Security:** ใช้ HTTPS ทั้งฝั่ง API และ App
- **Authentication:** ใช้ JWT (Bearer Token)
- **Performance:** ต้องสามารถรองรับผู้ใช้หลักพันคน (MVP) ได้ โดยไม่ต้องเปลี่ยน architecture
- **Scalability:** ออกแบบให้สามารถ scale แยก service ได้ในอนาคต
- **Localization:** รองรับภาษาไทยเป็นหลัก (อนาคตอาจเพิ่มภาษาอังกฤษ)
- **Time zone:** เก็บเวลาทุกอย่างเป็น UTC ใน DB และให้ฝั่ง Client แสดงผลตาม timezone ของอุปกรณ์

---

## 5. Out of Scope (สำหรับ MVP)

- Social login (Google / Apple)
- Web dashboard สำหรับ desktop
- ระบบแชร์ข้อมูลให้หมอ / คุณครู
- ระบบ monetization (subscription, payment gateway) – วางโครงได้ แต่ไม่ต้อง implement เต็ม
