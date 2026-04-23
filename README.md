# cronjob-disable

ตรวจสอบและแก้ไข `DISABLE_WP_CRON` ในไฟล์ `wp-config.php` ทุกเว็บบน cPanel server

---

## ทำไมต้องใช้ script นี้?

WordPress มีระบบ wp-cron ที่ทำงานทุกครั้งที่มี visitor เข้าเว็บ (default)  
บน server ที่มีเว็บหลายพัน domain → กิน resource มาก

**แนวทางที่ดีกว่า:**
- ใส่ `define('DISABLE_WP_CRON', true);` ใน `wp-config.php` ทุกเว็บ → ปิด visitor trigger
- เปิด cron เฉพาะเว็บที่ต้องการ → ผ่าน **cPanel → Cron Jobs** แบบ manual

---

## สิ่งที่ script นี้ทำ

1. **Scan** — อ่านโดเมนทั้งหมดจาก `/etc/userdomains`
2. **Summary** — แสดงว่ากี่โดเมนต้องแก้ / ปิดแล้วกี่โดเมน
3. **Confirm** — ถามยืนยัน `y/n` ก่อนแก้ไข
4. **Fix** — ใส่ `DISABLE_WP_CRON = true` ให้เฉพาะที่ยังไม่มี
5. **Backup** — backup `wp-config.php` ก่อนแก้ทุกครั้ง
6. **Rollback** — restore อัตโนมัติถ้าแก้ไม่สำเร็จ

---

## โครงสร้าง path ที่รองรับ

```
/home/USERNAME/public_html/DOMAIN/wp-config.php   ← แบบที่ 1
/home/USERNAME/DOMAIN/wp-config.php               ← แบบที่ 2
```

---

## วิธีใช้งาน (One-liner ไม่ต้องโหลดลง server)

### รันทุก user (ทั้ง server)
```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress.sh)
```

### รันเฉพาะ user เดียว
```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress.sh) y2026m04ns504
```

### รันหลาย user พร้อมกัน
```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress.sh) jan2026newkey y2026m03sv01 y2026m04ns504
```

---

## วิธีติดตั้ง (ถ้าต้องการเก็บไว้ใน server)

```bash
curl -o /usr/local/sbin/edit-disable-wordpress.sh \
  https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress.sh

chmod +x /usr/local/sbin/edit-disable-wordpress.sh
```

```bash
# รันทุก user
bash /usr/local/sbin/edit-disable-wordpress.sh

# รันเฉพาะ user เดียว
bash /usr/local/sbin/edit-disable-wordpress.sh y2026m04ns504

# รันหลาย user
bash /usr/local/sbin/edit-disable-wordpress.sh jan2026newkey y2026m03sv01 y2026m04ns504
```

---

## ตัวอย่าง Output

```
======================================================
   DISABLE_WP_CRON - Auto Scanner & Fixer v2.3
======================================================

🌐 สแกนทุก user

🔍 กำลังสแกน...

======================================================
  📊 สรุปผลการสแกน
======================================================

❌ ยังไม่ได้ปิด (3 โดเมน):
------------------------------------------------------
  1. fafa138.co (y2026m04ns504) [public_html] [ไม่มีเลย]
  2. flik84.org (y2026m04ns504) [public_html] [ไม่มีเลย]
  3. tga99.net  (y2026m04ns504) [public_html] [มีแต่เป็น false]

✅ ปิดแล้ว (5712 โดเมน) — skip

======================================================
  🔴 ต้องแก้ไข  : 3 โดเมน
  🟢 skip แล้ว  : 5712 โดเมน
  ⚪ ไม่มี WP   : 0 โดเมน
  📁 ทั้งหมด    : 5715 โดเมน
======================================================

ต้องการเพิ่ม define('DISABLE_WP_CRON', true)
ให้ 3 โดเมน ที่ยังไม่ได้ปิดหรือไม่?

  y = ดำเนินการแก้ไข
  n = ยกเลิก

ยืนยัน (y/n): y

🔧 กำลังแก้ไข...

  🔧 fafa138.co (y2026m04ns504) [public_html] ... ✅
  🔧 flik84.org (y2026m04ns504) [public_html] ... ✅
  🔧 tga99.net  (y2026m04ns504) [public_html] ... ✅

======================================================
  📊 สรุปผลการแก้ไข
======================================================
  ✅ สำเร็จ   : 3 โดเมน
  ❌ ล้มเหลว  : 0 โดเมน
  🟢 skip     : 5712 โดเมน
  📁 ทั้งหมด  : 5715 โดเมน
======================================================

💡 Backup ไว้ที่: wp-config.php.bak_YYYYMMDDHHMMSS
```

---

## หมายเหตุสำคัญ

| เรื่อง | รายละเอียด |
|---|---|
| ไม่สร้างซ้ำ | เช็คก่อนเสมอ ถ้ามีแล้วจะ skip ทันที |
| Backup | สร้างไว้ที่ `wp-config.php.bak_YYYYMMDDHHMMSS` |
| Rollback | ถ้าแก้ไม่สำเร็จ restore backup อัตโนมัติ |
| Fast skip | โดเมนที่ปิดแล้ว → `grep -q` หยุดทันที ไม่อ่านทั้งไฟล์ |
| Filter user | ระบุ username เป็น argument เพื่อแก้เฉพาะ user นั้น |

---

## การทำงานของ DISABLE_WP_CRON

```
DISABLE_WP_CRON = true   → ปิด visitor trigger (ประหยัด resource)
DISABLE_WP_CRON = false  → WordPress เรียก wp-cron ทุก page load (default)
```

> **สำคัญ:** `DISABLE_WP_CRON = true` ไม่ได้ปิด scheduled tasks  
> แค่บอกว่า "อย่ารันเองตอน visitor เข้า ให้รอ cron มาเรียกแทน"  
> ถ้าต้องการให้ scheduled tasks ทำงาน → ต้องใส่ cron ใน **cPanel → Cron Jobs** ด้วย

---

## วิธีเปิด wp-cron สำหรับเว็บที่ต้องการ

ไปที่ **cPanel → Cron Jobs** ของ user นั้น แล้วเพิ่ม:

```
Minute : 0,30
Hour   : *
Day    : *
Month  : *
Weekday: *
Command: cd /home/USERNAME/public_html/DOMAIN && /usr/local/bin/php /home/USERNAME/public_html/DOMAIN/wp-cron.php
```

---

## ข้อควรระวัง

- **ห้ามใช้ WP Toolkit toggle "Take over wp-cron.php"** — จะสร้าง cron ซ้ำซ้อนอัตโนมัติ
- script นี้รันในสิทธิ์ **root** เท่านั้น
- รองรับเฉพาะ **cPanel server** ที่มี `/etc/userdomains` และ `/etc/trueuserdomains`

---

## Changelog

| Version | รายละเอียด |
|---|---|
| v2.3 | เพิ่ม filter by user + fast skip ด้วย grep -q |
| v2.2 | เพิ่ม confirm y/n ก่อนแก้ไข |
| v2.1 | ใช้ /etc/userdomains แทน find, รองรับ 2 path structure |
| v1.0 | เวอร์ชันแรก |
