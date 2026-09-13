# 📊 Procurement Performance Dashboard (Delivery Date Basis)

## 🎯 วัตถุประสงค์ (Objective)
วิเคราะห์ประสิทธิภาพงานจัดซื้อและประเมินผลการดำเนินงานของซัพพลายเออร์ โดยอ้างอิงจากวันที่ส่งมอบสินค้าจริง (Delivery Date) เพื่อค้นหาโอกาสในการลดต้นทุน ควบคุมคุณภาพสินค้า และตรวจสอบความแม่นยำในการปฏิบัติตามมาตรฐานของคู่ค้า

---

## 📋 รายละเอียดโครงการ (Project Description)
แดชบอร์ด Power BI แบบโต้ตอบที่ถูกออกแบบมาเพื่อประเมินผลงานของซัพพลายเออร์ เจาะลึกหาความสัมพันธ์ระหว่างคุณภาพสินค้ากับสถานะ Compliance และคำนวณผลกระทบทางการเงินที่แท้จริงจากอัตราสินค้าชำรุด โดยโครงการนี้อ้างอิงจากวันที่ส่งมอบสินค้าจริง (Delivery Date) เพื่อให้สะท้อนประสิทธิภาพจริงของหน้างานและโลจิสติกส์ และคำนวณเฉพาะ order ที่มีการส่งมอบเกิดขึ้นจริง (Delivered / Partially Delivered) เท่านั้น เพื่อไม่ให้ order ที่ถูกยกเลิกหรือยังไม่ส่งของมาปนกับตัวเลขทางการเงิน

ในส่วนของกระบวนการทำ Data Engineering, การจัดการข้อมูลสูญหาย (Missing Values) และการเตรียม Data Pipeline ทั้งหมดถูกขับเคลื่อนด้วย **Python (Pandas)** โดยใช้งานชุดข้อมูลการจัดซื้อจาก Kaggle

---

## 👩‍💻 บทบาทความรับผิดชอบ (Role & Responsibilities)
* ทำความสะอาดและแปลงข้อมูลด้วย Python (Pandas) เพื่อจัดการข้อมูลที่สูญหายและข้อมูลที่ไม่สอดคล้องกัน (negative lead time, defect > quantity)
* สร้างตัวชี้วัดหลัก (KPIs) และมาตรวัดเชิงลึก (Advanced DAX Measures) พร้อมกำหนดตรรกะทางธุรกิจว่าควรนับ order สถานะใดบ้างในแต่ละ measure
* ออกแบบและพัฒนาแดชบอร์ดแบบโต้ตอบ (Interactive Dashboard) ด้วย Power BI จำนวน 3 หน้า
* ทำการวิเคราะห์เจาะลึก (Root Cause Analysis) เพื่อตรวจสอบว่าอัตรากำไรจาก Negotiated Price ยังคุ้มค่าอยู่จริงหรือไม่ เมื่อหักลบมูลค่าความเสียหายจากของเสีย

---

## 📊 โครงสร้างภาพรวมแดชบอร์ด (Dashboard Overview — 3 Pages)

**Page 1 — Executive Overview**: Total Savings, Savings %, Total Qty, Avg Lead Time, Defect Rate % (by Month, by Item_Category), Total Savings (by Supplier, by Quarter), Compliance donut

**Page 2 — Supplier Performance**: Total Qty / Total Defect Qty / Defect Rate % by Supplier, Matrix (Supplier × Item_Category) พร้อม conditional formatting, Defect Cost & Defect Cost % of Spend, Avg Lead Time by Supplier

**Page 3 — Data Quality Overview**: Total Rows, % Imputed, % Missing Delivery Date, Imputed Rows by Supplier, Missing Delivery Date Rows by Order_Status, ตารางสรุปปัญหา/สาเหตุ/วิธีแก้ไข

---

## 🧮 หลักการคำนวณสำคัญ (Business Logic)

ข้อมูลมี `Order_Status` 4 แบบ: `Delivered`, `Partially Delivered`, `Pending`, `Cancelled`

**ตัดสินใจว่า**: Measure ด้านการเงินและคุณภาพ (Savings, Spend, Defect Cost, Defect Rate, Compliance) **นับเฉพาะ Delivered + Partially Delivered เท่านั้น** เพราะ Cancelled ไม่มีการซื้อขายเกิดขึ้นจริง และ Pending ยังไม่มีของมาส่งให้ประเมินคุณภาพได้ ส่วน Total Qty ยังคงนับทุก order เพื่อตอบคำถาม "สั่งซื้อไปเท่าไหร่" โดยไม่สนใจผลลัพธ์

---

## 📈 ผลลัพธ์จากการวิเคราะห์เชิงลึก (Key Findings)

> วิธีการ: ตรวจสอบ order ที่มีอัตราสินค้าชำรุด (Defect Rate ระดับรายออเดอร์ = Defective_Units ÷ Quantity) สูงที่สุดในแต่ละปี เพื่อดูว่าเกิดจากอะไร และกระทบต้นทุนจริงแค่ไหน

### 🔴 ปี 2022: Order ที่มีอัตราของเสียสูงสุด — Delta_Logistics
* **เหตุการณ์**: Order `PO-00470` หมวด **Raw Materials** ส่งมอบวันที่ 18 ต.ค. 2022 มีอัตราชำรุดสูงถึง **74.3%** (171 จาก 230 ชิ้น)
* **Lead Time**: 17 วัน (สูงกว่าค่าเฉลี่ยทั้งปีที่ 10.6 วัน) — ความล่าช้าในการขนส่งอาจเป็นปัจจัยร่วมที่ทำให้สินค้าเสียหายระหว่างทาง
* **ผลกระทบการเงิน**: มูลค่าความเสียหายจากของเสีย (Defect Cost) = **$2,223** ในขณะที่ order นี้ทำ saving ได้เพียง **$43.7** → **ขาดทุนเนื้อในของ order นี้ -$2,179.3**
* **Compliance**: order นี้ได้ **No** — ระบบตรวจจับความผิดปกตินี้ได้ถูกต้อง

### 🔴 ปี 2023: Order ที่มีอัตราของเสียสูงสุด — Delta_Logistics
* **เหตุการณ์**: Order `PO-00020` หมวด **Raw Materials** ส่งมอบวันที่ 24 ก.ย. 2023 อัตราชำรุดพุ่งถึง **95%** (171 จาก 180 ชิ้น) — เกือบทั้งล็อตใช้งานไม่ได้
* **Lead Time**: 15 วัน (สูงกว่าค่าเฉลี่ยทั้งปีที่ 10.5 วัน)
* **ผลกระทบการเงิน**: Defect Cost = **$7,821.5** ในขณะที่ saving ทำได้แค่ **$1,233** → **ขาดทุนเนื้อใน -$6,588.5** เป็น order ที่สร้างความเสียหายสูงสุดในชุดข้อมูลทั้งหมด
* **Compliance**: ได้ **No** เช่นกัน

### 🔴 ปี 2024 (ข้อมูล 1 เดือน): Order ที่มีอัตราของเสียสูงสุด — Beta_Supplies
* **เหตุการณ์**: Order `PO-00386` หมวด **Packaging** ส่งมอบวันที่ 11 ม.ค. 2024 อัตราชำรุด **34%** (103 จาก 303 ชิ้น)
* **Lead Time**: 20 วัน (สูงกว่าค่าเฉลี่ยของช่วงนี้ที่ ~14.4 วัน อย่างชัดเจน) — ล่าช้ากว่าปกติเกือบเท่าตัว
* **ผลกระทบการเงิน**: Defect Cost = **$1,335.9** ในขณะที่ saving ทำได้ **$469.7** → **ขาดทุนเนื้อใน -$866.3**
* **Compliance**: ได้ **No**

**ข้อสังเกตร่วมของทั้ง 3 เคส**: Delta_Logistics ครองอันดับ worst-case ถึง 2 ใน 3 ปี ยืนยันสถานะ "ซัพพลายเออร์ที่ต้องเฝ้าระวังสูงสุด" ตรงกับที่เห็นในภาพรวม Supplier Performance (defect rate เฉลี่ยทั้งปีของ Delta_Logistics สูงสุดในกลุ่ม ~15%)

---

## 🔍 ข้อค้นพบเกี่ยวกับความสัมพันธ์ Compliance ↔ Defect Rate

ทดสอบ correlation ระหว่างตัวแปรกับสถานะ Compliance = No จากข้อมูลทั้งชุด (เฉพาะ Realized orders):

| ตัวแปร | Correlation กับ Compliance = No |
|---|---|
| Defect Rate (รายออเดอร์) | **+0.32** (มีความสัมพันธ์ชัดเจน) |
| Lead Time | -0.04 (แทบไม่มีความสัมพันธ์) |

**สรุป**: ตรงข้ามกับสมมติฐานเบื้องต้นที่คาดว่า Compliance อาจผูกกับความเร็วในการส่งของเป็นหลัก — ข้อมูลจริงแสดงว่า **อัตราของเสียมีความสัมพันธ์กับสถานะ Non-Compliance มากกว่าความล่าช้าในการส่งของ** และยืนยันได้จาก 3 order ที่ defect สูงสุดของแต่ละปีข้างต้น ทั้ง 3 order ได้ Compliance = No ตรงกันหมด — ระบบดูจะจับปัญหาคุณภาพได้ค่อนข้างดีอยู่แล้ว ไม่ใช่ช่องโหว่ตามที่เคยตั้งสมมติฐานไว้ในตอนแรก

---

## 📊 บทสรุปภาพรวมทางการเงิน (Financial Summary — Realized Orders Only)

| รายการ | มูลค่า |
|---|---|
| Total Spend | $39,570,053.95 |
| Total Savings (จาก Negotiated Price) | **$3,093,154.99** |
| Savings % | **7.82%** |
| Total Defect Cost | **$2,758,202.60** |
| Defect Cost % of Spend | **6.97%** |
| **Net Savings (Savings − Defect Cost)** | **+$334,952.39** |
| Compliance % (Yes) | 82.15% (520 จาก 633 order) |

**ข้อสรุปสำคัญ**: ยอด Total Savings $3.09M **ยังคงเป็นกำไรสุทธิบวกจริง** หลังหักมูลค่าความเสียหายจากของเสียแล้ว (Net Savings = +$334,952) — แต่สิ่งที่ควรเป็นสัญญาณเตือนคือ **Savings % (7.82%) กับ Defect Cost % of Spend (6.97%) ใกล้เคียงกันมาก** ต่างกันเพียง 0.85 จุดเปอร์เซ็นต์ หมายความว่า **เกือบทั้งหมดของกำไรที่ได้จากการต่อรองราคา ถูกกลืนไปกับต้นทุนของเสียเกือบหมด** หากอัตราของเสียเพิ่มขึ้นอีกเพียงเล็กน้อย (โดยเฉพาะจาก Delta_Logistics ที่มี defect rate สูงสุด) องค์กรมีความเสี่ยงที่จะขาดทุนสุทธิจากการจัดซื้อทั้งระบบ

---

## ⚠️ ข้อจำกัดของข้อมูล (Data Quality Notes)

* `Defective_Units` หาย 136 แถว (17.5%) — เติมด้วย median แยกตาม (Supplier, Item_Category)
* `Delivery_Date` หาย 87 แถว — 73 แถวเกิดจาก lead time ติดลบ (ข้อมูลผิดปกติ, เติมค่ากลับแล้ว), 14 แถวเป็น order สถานะ Cancelled/Pending ที่ไม่ควรมีวันที่ส่งของอยู่แล้ว (ปล่อยว่างตามความจริง ไม่ impute)
* **พบ 1 แถว (`PO-00462`, Delta_Logistics, MRO) ที่ Defective_Units (161) มากกว่า Quantity (148)** ซึ่งเป็นไปไม่ได้ในทางตรรกะ — เป็นจุดที่ควรตรวจสอบกับต้นทางข้อมูลเพิ่มเติม ไม่ได้ถูกใช้ในการจัดอันดับ worst-case ข้างต้นเพื่อไม่ให้ผลลัพธ์บิดเบือน

---

## 🧠 สิ่งที่ได้เรียนรู้ (Learnings)
* พัฒนาทักษะการทำความสะอาดและตรวจสอบความถูกต้องของข้อมูล (Data Validation) ด้วย Python รวมถึงการตรวจจับ anomaly ที่ตัวเลขผิดตรรกะทางธุรกิจ (lead time ติดลบ, defect > quantity)
* เรียนรู้ที่จะไม่เชื่อตัวเลขภาพรวมแบบผิวเผิน แต่ต้อง**ตรวจสอบทุกตัวเลข/ตัวอย่างที่ยกมาใช้กับข้อมูลจริงเสมอ** ก่อนสรุปเป็น insight — แม้แต่ข้อสรุปของตัวเองในรอบก่อนก็ต้องกลับมาทวนซ้ำได้
* เข้าใจความสำคัญของการกำหนดขอบเขตข้อมูล (Order_Status filter) ก่อนคำนวณ measure ทางการเงิน เพื่อไม่ให้ order ที่ยังไม่เกิดธุรกรรมจริงมาปนกับตัวเลขผลประกอบการ

---

## 🚀 แนวทางพัฒนาในอนาคต (Future Development)
* เพิ่ม measure `Net Savings` (Savings − Defect Cost) เป็น KPI หลักบนหน้า Executive Overview แทนที่จะโชว์แค่ Total Savings เพียงอย่างเดียว เพื่อสะท้อนกำไรที่แท้จริงหลังหักความเสียหาย
* ตั้ง threshold เตือนอัตโนมัติ: หาก Defect Cost % of Spend ของ supplier ใดเข้าใกล้หรือเกิน Savings % ของ supplier นั้น ให้ flag เป็นความเสี่ยงที่ต้องทบทวนสัญญา
* ตรวจสอบและแก้ไขแถวข้อมูลที่ผิดตรรกะ (Defective_Units > Quantity) ร่วมกับต้นทางข้อมูล ก่อนนำไปใช้งานจริง

---

## 📁 Files

| File | รายละเอียด |
|---|---|
| `Procurement_KPI_Analysis_Dataset.csv` | ข้อมูลดิบ |
| `fix_cleaned_procurement_dataset.ipynb` | Notebook ทำความสะอาดข้อมูล |
| `Fix_Procurement_KPI_Analysis_Cleaned_latest.csv` | ข้อมูลที่ clean แล้ว พร้อมใช้ |
| `fix_latest_Dash.pbix` | ไฟล์ Power BI Dashboard (3 หน้า) |

## 🛠️ Tools
Python (pandas, numpy) สำหรับ data cleaning & imputation, Power BI (Power Query, DAX) สำหรับ data modeling & visualization