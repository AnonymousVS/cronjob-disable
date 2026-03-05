# cronjob-disable

> ตรวจสอบและปิด **DISABLE_WP_CRON** ใน `wp-config.php` ทุกเว็บใน cPanel/WHM server  
> มี 2 script แยกตามหน้าที่ — ตรวจสอบอย่างเดียว และ ตรวจสอบพร้อมแก้ไขอัตโนมัติ

---

## ทำไมต้องปิด WP Cron?

WordPress Cron ปกติจะรันทุกครั้งที่มีคนเข้าเว็บ (HTTP request trigger)  
ถ้ามีเว็บจำนวนมากบน server เดียวกัน → **PHP process พุ่งสูง → server ช้า**

การตั้ง `define('DISABLE_WP_CRON', true);` ใน `wp-config.php` จะปิด HTTP trigger  
แล้วใช้ **Real Cron Job** (cPanel Cron) แทน → ประหยัด resource มากกว่า

---

## Script ที่มีทั้งหมด

| ไฟล์ | หน้าที่ |
|------|---------|
| `check-disable-wordpress` | ✅ ตรวจสอบอย่างเดียว ไม่แก้ไขอะไร |
| `edit-disable-wordpress` | 🔧 ตรวจสอบ + แก้ไขอัตโนมัติทันที |

---

## 1. check-disable-wordpress

### ทำอะไร
- สแกน `wp-config.php` ทุกเว็บ
- แสดงรายชื่อเว็บที่ **ยังไม่ได้ปิด** และ **ปิดแล้ว**
- แสดงคำสั่ง `sed` สำหรับแก้ไขเอง (แต่ไม่รันให้)
- **ไม่แก้ไขไฟล์ใดๆ** — ดูข้อมูลอย่างเดียว

### คำสั่งรัน

```bash
# สแกน /home ทั้งหมด (default)
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/check-disable-wordpress)


```
# หรือระบุ path เอง
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/check-disable-wordpress) /home/jan2026newkey
### ตัวอย่างผลที่ได้

```
======================================================
   ตรวจสอบ DISABLE_WP_CRON ทุกเว็บใน /home
======================================================

❌ เว็บที่ยังไม่ได้ปิด (3 เว็บ):
------------------------------------------------------
  1. example.org  [ไม่มีค่านี้เลย]
  2. demo.com     [ตั้งค่าแต่เป็น false]
  3. test.net     [ไม่มีค่านี้เลย]

✅ เว็บที่ปิด DISABLE_WP_CRON แล้ว (5 เว็บ):
------------------------------------------------------
  1. site1.com
  2. site2.org
  ...

======================================================
  📊 สรุป:
  🔴 ยังไม่ปิด : 3 เว็บ
  🟢 ปิดแล้ว  : 5 เว็บ
  📁 ทั้งหมด  : 8 เว็บ
======================================================
```

### สถานะที่แสดง

| สถานะ | ความหมาย |
|-------|---------|
| `[ไม่มีค่านี้เลย]` | ไม่มี `DISABLE_WP_CRON` ใน `wp-config.php` เลย |
| `[มีค่าแต่เป็น false]` | มี `define('DISABLE_WP_CRON', false)` — ตั้งค่าไว้แต่ยังไม่ได้ปิด |
| ✅ ปิดแล้ว | มี `define('DISABLE_WP_CRON', true)` — ปิดถูกต้องแล้ว |

---

## 2. edit-disable-wordpress

### ทำอะไร
- สแกน `wp-config.php` ทุกเว็บ (**รองรับ /home, /home2, /home3 ... อัตโนมัติ**)
- แสดงรายชื่อก่อนแก้ไข
- **Backup** `wp-config.php` ก่อนแก้ทุกครั้ง
- แก้ไข `DISABLE_WP_CRON` → `true` อัตโนมัติ
- ถ้าแก้ไม่สำเร็จ → **restore backup อัตโนมัติ**
- แสดงสรุปผลหลังแก้ไขเสร็จ

### คำสั่งรัน

```bash
# สแกนและแก้ไขทุก /home, /home2, /home3 ... อัตโนมัติ
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress)

# หรือระบุ path เอง
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress) /home/jan2026newkey
```

### ตัวอย่างผลที่ได้

```
======================================================
   DISABLE_WP_CRON - Auto Scanner & Fixer
======================================================

📂 Home directories ที่พบ:
   ✔ /home

🔍 กำลังสแกน: /home

❌ เว็บที่ยังไม่ได้ปิด (3 เว็บ):
------------------------------------------------------
  1. example.org [/home]  [ไม่มีค่านี้เลย]
  2. demo.com    [/home]  [มีค่าแต่เป็น false]

✅ เว็บที่ปิดแล้ว (5 เว็บ):
------------------------------------------------------
  1. site1.com [/home]

======================================================
  📊 สรุปก่อนแก้ไข:
  🔴 ยังไม่ปิด : 3 เว็บ
  🟢 ปิดแล้ว  : 5 เว็บ
  📁 ทั้งหมด  : 8 เว็บ
======================================================

🔧 กำลังแก้ไข wp-config.php อัตโนมัติ...
  🔧 example.org [/home] ... ✅ สำเร็จ
  🔧 demo.com    [/home] ... ✅ สำเร็จ

======================================================
  📊 สรุปหลังแก้ไข:
  ✅ แก้ไขสำเร็จ  : 3 เว็บ
  ❌ แก้ไขล้มเหลว : 0 เว็บ
  🟢 ปิดอยู่แล้ว : 5 เว็บ
  📁 ทั้งหมด      : 8 เว็บ
======================================================

💡 Backup: wp-config.php.bak_wpcron_YYYYMMDDHHMMSS
```

### สถานะหลังแก้ไข

| สถานะ | ความหมาย |
|-------|---------|
| ✅ แก้ไขสำเร็จ | เพิ่ม/แก้ `DISABLE_WP_CRON = true` เรียบร้อย |
| ❌ แก้ไขล้มเหลว | แก้ไม่สำเร็จ (permission ไม่พอ) → restore backup อัตโนมัติ |
| 🟢 ปิดอยู่แล้ว | ไม่ต้องแก้ไข |

### Backup ไฟล์
ทุกครั้งที่แก้ไข script จะ backup อัตโนมัติ:
```
wp-config.php.bak_wpcron_20260305142500
```

---

## บันทึก Output เป็นไฟล์

Script ทั้ง 2 ตัวแสดงผลทางหน้าจออย่างเดียว ถ้าต้องการเก็บ log ให้ redirect เอาเองครับ:

### บันทึก output ไปไฟล์
```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/check-disable-wordpress) \
    | tee /var/log/check-disable-wordpress.log
```

```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress) \
    | tee /var/log/edit-disable-wordpress.log
```

### ดู log ที่บันทึกไว้
```bash
cat /var/log/check-disable-wordpress.log
cat /var/log/edit-disable-wordpress.log
```

### ดูเฉพาะเว็บที่ยังไม่ปิด
```bash
grep "ยังไม่ได้ปิด\|ไม่มีค่า\|false" /var/log/check-disable-wordpress.log
```

### ดูเฉพาะเว็บที่แก้ไขสำเร็จ
```bash
grep "สำเร็จ" /var/log/edit-disable-wordpress.log
```

### ดูเฉพาะเว็บที่แก้ไขล้มเหลว
```bash
grep "ล้มเหลว" /var/log/edit-disable-wordpress.log
```

### นับจำนวน
```bash
grep -c "สำเร็จ" /var/log/edit-disable-wordpress.log
grep -c "ล้มเหลว" /var/log/edit-disable-wordpress.log
```

---

## แนะนำลำดับการใช้งาน

```
1. รัน check-disable-wordpress ก่อน → ดูว่ามีเว็บไหนต้องแก้บ้าง
2. ถ้าพอใจกับรายการ → รัน edit-disable-wordpress เพื่อแก้ไขอัตโนมัติ
3. รัน check-disable-wordpress อีกรอบ → verify ว่าทุกเว็บปิดแล้ว
```

---

## License

MIT
