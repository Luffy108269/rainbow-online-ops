# CLAUDE.md — Rainbow Online (Ops)

> Claude Code อ่านไฟล์นี้อัตโนมัติทุกครั้งที่เปิดโปรเจกต์นี้
> อยู่ใน repo → push แล้วตามไปทุกเครื่องเอง
> เว็บจริง: https://luffy108269.github.io/rainbow-online-ops/

---

## 🚦 พิธีกรรมเริ่มงาน/เลิกงาน

- **เริ่มงาน:** สั่ง "git pull ก่อน แล้วอ่าน tracker บอกว่าค้างตรงไหน"
- **เลิกงาน:** สั่ง "อัปเดต tracker แล้ว commit + push"
- **กฎ:** pull ก่อนเริ่ม, push ก่อนเลิก, ห้ามแก้ 2 เครื่องพร้อมกันไม่ push

---

## 🔴 กฎเหล็ก (ห้ามข้าม)

### Git — ใช้หลายเครื่อง (3 คอม + มือถือ)
1. **pull ก่อนเริ่มเสมอ** — รัน `git pull` ก่อนทำอะไร ดึงเวอร์ชันล่าสุดจากเครื่องอื่น
2. **commit + push ก่อนเลิกเสมอ** — ก่อนปิดงาน/ย้ายเครื่อง ต้อง push ทุกครั้ง
3. **ห้ามแก้ 2 เครื่องพร้อมกันโดยไม่ push** — จะ conflict เผลอเมื่อไรเตือนเจ้าของทันที
4. push เสร็จ บอกด้วยว่า GitHub Pages จะอัปเดตใน 1-2 นาที (เว็บ = index.html บน Pages นี้)

### BUILD stamp — ต้องตรง 3 จุดก่อน push ทุกครั้ง
- `BUILD` (index.html) — เวอร์ชัน frontend เช่น `B3.105`
- `FRONT_VER` (index.html) — ต้องตรงกับ build ใน backend เช่น `3.93`
- `build` ใน `doGet` (Code.gs) — ต้องตรงกับ FRONT_VER
> แก้อะไรที่กระทบทั้งสองฝั่ง ต้องขยับเลขให้ครบทั้ง 3 จุด

### ก่อน push โค้ด — เช็คเสมอ
- `node --check` ผ่าน (ไม่มี syntax error)
- ไม่มีฟังก์ชันชื่อซ้ำ (ทุกตัวต้องมีนิยามเดียว) — เคยเจอ g2 หาย/ซ้ำแล้วตายเงียบ
- health check ควรเช็คฟังก์ชันที่ "ถูกเรียกแต่ไม่มีตัวตน" ด้วย

### Apps Script (Code.gs) — เฉพาะเว็บนี้
1. หลังแก้ Code.gs → **Deploy → Manage deployments → ✏️ Edit → New version → Deploy** เท่านั้น (ไม่งั้นเว็บใช้โค้ดเก่า)
2. แก้ส่วนที่แตะ DriveApp / permissions ใหม่ → รัน **`initDrivePermission`** ใน editor + approve permissions ก่อน ไม่งั้น**ฟังก์ชันเงียบ ไม่ error ไม่ทำงาน**
   > ⚠️ เว็บนี้ใช้ `initDrivePermission` — ไม่ใช่ authorizeFromEditor (อันนั้นของ re-order)
3. เตือนเจ้าของทุกครั้งที่แก้ Code.gs ว่าต้อง deploy + รัน initDrivePermission

### Ketshop MCP — กฎการเขียนข้อมูล (Luffy สั่ง 23 ก.ค. 2569)
- **อ่านข้อมูล Ketshop ได้อิสระ** (สต็อก/สินค้า/รายงาน/ออเดอร์)
- **เขียน/แก้อะไรเข้า Ketshop ต้องถาม Luffy ยืนยัน 3 ครั้งก่อนเสมอ** — ไม่มีข้อยกเว้น (สต็อก Ketshop ผูกหน้าร้านจริง Shopee/Lazada/TikTok)
- ฟีเจอร์เว็บแอปที่เขียนเข้า Ketshop ต้องมี confirm 3 ชั้นใน UI เช่นกัน
- Shopee = ช่องทาง master ของร้าน · ราคาใน Ketshop ไม่ใช่ราคาขายจริงบน marketplace
- **⚖️ มีสัญญา API/MCP กับ Ketshop — ปฏิบัติตามเคร่งครัด เป็นกฎเหล็ก** · ตัวสัญญาเป็นความลับ **ห้ามใส่เนื้อหาสัญญาลง repo นี้ (public) เด็ดขาด** · checklist ฉบับเต็มอยู่ Google Drive โฟลเดอร์โปรเจกต์ ไฟล์ `ketshop-compliance-checklist.md` — อ่านก่อนแตะงาน Ketshop ทุกครั้ง · หัวใจ: ห้าม pen-test/ห้ามเกิน rate limit/ห้ามแชร์คีย์/ห้าม token ในแชท/หมุนคีย์ทุก 90 วัน/log การเรียก API ≥90 วัน

### กฎเฉพาะระบบ (บทเรียนที่เจ็บมาแล้ว)
- **อ่านโค้ดเดิมก่อนแก้เสมอ** — ไฟล์ใหญ่ อย่าเดา
- frontend ส่ง/ตั้งชื่อ **`index.html` เสมอ** — ห้าม index_build2.html (อัปขึ้น Pages ตรง ๆ)
- CSV บัญชี: คอลัมน์ล็อกถาวร **ห้ามมี comma ในตัวเลข**
- ทดสอบบนเว็บจริง `https://luffy108269.github.io/...` เท่านั้น — **ห้าม file://** (เจอบทเรียน 15 ก.ค.)
- แก้โค้ดผ่านสคริปต์/tool ไม่ใช่พิมพ์มือทับทั้งไฟล์

---

## 🟢 วิธีทำงานที่ต้องการ

- แก้ไฟล์บนเครื่องโดยตรง **ห้ามให้เจ้าของ copy-paste เอง**
- อยากดูผล → เปิด **local preview ผ่าน server** (ไม่ใช่ file://) ไม่ต้อง push เพื่อดู
- แก้เสร็จสรุปสั้น ๆ ว่าแก้อะไร + ขยับ build เลขอะไรบ้าง
- งานใหญ่/ไม่ชัด → ถามให้เคลียร์ก่อน (แนว grill-me) อย่าเดา

---

## 📋 การต่อเนื่องเมื่อแชทเต็ม

ไฟล์ tracker: **`RB_Ops_Master_Tracker.md`** (อยู่ใน repo นี้)

- **ปลายแชททุกครั้ง** ก่อน push → อัปเดต `RB_Ops_Master_Tracker.md` ให้เป็นสถานะล่าสุด (ทำอะไรเสร็จ ค้างตรงไหน build เท่าไร)
- **ต้นแชทใหม่ทุกครั้ง** → อ่าน tracker ก่อน แล้วสรุปให้ฟังว่าค้างตรงไหน ก่อนเริ่ม
- ถ้าเจ้าของบอก "ต่องาน" / "แชทเก่าเต็ม" → อ่าน tracker แล้วสรุปทันที
> เลิกใช้วิธีเดิม (อัปเข้า Project Knowledge มือ) แล้ว — tracker อยู่ใน repo, git จัดการ sync ให้เอง

---

## 📦 เกี่ยวกับโปรเจกต์นี้

- **ชื่อ:** RB Online Ops — ระบบปฏิบัติการ Rainbow Online
- **เว็บ:** https://luffy108269.github.io/rainbow-online-ops/
- **สถาปัตยกรรม:** frontend `index.html` (GitHub Pages) ยิงไป backend Google Apps Script (`Code.gs`) + Google Sheets เป็นฐานข้อมูล
- **โมดูลหลัก:** ของเข้า 2 ทาง · คัมภีร์สินค้า · ราคา 5 ตัวเลข · บัญชี 2 บริษัท · Ketshop Queue · เช็คสต็อก · นำเข้า SKU · Low Stock Alert · LINE OA
- **backend authorize:** `initDrivePermission`
- **ไฟล์สำคัญ:**
  - `index.html` — frontend ทั้งหมด (BUILD + FRONT_VER อยู่บนสุด)
  - `Code.gs` — backend ทั้งหมด (build อยู่ใน doGet)
- **แผนงาน:** ดู tracker — ลำดับเฟส A -> B -> C -> D -> E
