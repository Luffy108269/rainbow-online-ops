# ข้อความถึง Ketshop Support (ส่งได้ทาง chat support หรือ sales@ketshopweb.com)

> ร้าน: Rainbow Silver · โดเมน: www.rainbowsilveronline.com
> เรื่อง: ขอ credentials สำหรับ OpenAPI + แจ้งบั๊ก + ขอ endpoint เพิ่ม (ใช้ MCP อยู่แล้ว ใช้งานได้ดี)

---

สวัสดีครับ ผมใช้ Ketshopweb MCP (เชื่อมกับ Claude) อยู่ และกำลังต่อระบบหลังบ้านของร้านเข้ากับ OpenAPI เพิ่มเติม มีเรื่องรบกวน 5 ข้อครับ:

## 1. ขอวิธีออก client credentials สำหรับ OpenAPI (สำคัญสุด)

ต้องการเรียก `https://openapi.ketshopweb.com` แบบ server-to-server จากระบบของร้านเอง (Google Apps Script) — endpoint `POST /auth/token` ต้องใช้ header `x-oapi-client-id` + `x-oapi-secret` แต่ผมลองใช้ API KEY / API SECRET จากหน้า "ส่วนเสริม" ในหลังบ้านแล้วได้ **HTTP 403 Unauthorized** ตลอด

คำถาม: client-id/secret สำหรับ OpenAPI ขอ/สร้างได้จากที่ไหนครับ? (ใน docs ของ `/oauth/v2/token` พูดถึง "API console" สำหรับ register app — เข้าได้ทางไหนครับ?)

## 2. แจ้งบั๊ก: POST /order/search_order ตอบ 422 ทั้งที่ข้อมูลถูกต้อง

เรียก `POST /order/search_order` (รวมถึง tool `search_orders` ใน MCP) ได้ **HTTP 422 "Expected union value"** ทุกครั้ง — ดูจาก error แล้ว ข้อมูลจริงของออเดอร์ส่งมาถึงแล้ว แต่ **schema validation ฝั่ง response ของระบบเข้มกว่าข้อมูลจริง** ทำให้ตีตกข้อมูลตัวเอง ตัวอย่าง:

- `data.0.delivery` มีค่า `{"id":7000,"name":"SPX Express","cod":false}` แต่ schema บังคับ `{id, type, logo}` → validate ไม่ผ่าน
- `data.0.paymenttype` มีค่า `{"id":-1,"name":"SPayLater"}` แต่ schema บังคับ `{id, name, account, status}` → validate ไม่ผ่าน

รบกวนแก้ schema ของ response ให้ตรงกับข้อมูลจริงครับ ตอนนี้ค้นออเดอร์ผ่าน API ไม่ได้เลย

## 3. ขอ endpoint: ราคาตามช่องทาง (อ่าน + แก้ไข)

ร้านใช้ฟีเจอร์ "ราคาตามช่องทาง" (Shopee/Lazada/TikTok คนละราคา) ในหลังบ้านอยู่ แต่หา endpoint ใน OpenAPI ไม่เจอ (`/product/setprice` เป็นราคาตามเลเวลสมาชิก) — อยากได้ API อ่าน/แก้ราคาตามช่องทางเป็นชุด เพราะค่าธรรมเนียม marketplace ปรับบ่อย ต้องไล่แก้ทีละ SKU ในหน้าเว็บซึ่งช้ามากครับ

## 4. ขอ endpoint: ดูว่าสินค้าแต่ละตัวเชื่อม/ลงขายช่องทางไหนบ้าง

ในหลังบ้านมีข้อมูลการเชื่อมสินค้ากับ Shopee/Lazada/TikTok อยู่แล้ว (หน้าส่วนเสริมของแต่ละ marketplace) แต่ object สินค้าจาก `/product/search` ไม่มีข้อมูลนี้ — อยากได้ field หรือ endpoint ที่บอกว่า SKU นี้ผูกกับ marketplace ไหนบ้างครับ

## 5. ขอ endpoint: อัปเดตรูปสินค้า

`PATCH /product/{sku}/basic` แก้ได้หลาย field แต่ไม่มีรูปภาพ — อยากได้ช่องทางอัปโหลด/เปลี่ยนรูปสินค้าผ่าน API ครับ

ขอบคุณครับ
