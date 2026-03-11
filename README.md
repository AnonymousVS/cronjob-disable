# DISABLE_WP_CRON - Auto Scanner & Fixer

สคริปต์สำหรับตรวจสอบและเพิ่ม `DISABLE_WP_CRON` ใน `wp-config.php` อัตโนมัติ
รองรับทุก path structure และหา home directory จากชื่อ cPanel user ได้เลย

## ปรับแก้ Complete 100% ใช้งานได้ทุก cPanel พร้อมกัน ที่ย้ายโฮสมา รวมถึงทั้ง /home /home /public_html

---

## 📌 วิธีใช้งาน

### 1. scan & แก้ไข user เดียว

```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress) cpanel1
```

---

### 2. scan & แก้ไข หลาย user พร้อมกัน

```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress) cpanel1 cpanel2
```

---

### 3. scan & แก้ไข ทุก user ในเครื่อง

```bash
bash <(curl -s https://raw.githubusercontent.com/AnonymousVS/cronjob-disable/main/edit-disable-wordpress)
```

---

## ✅ รองรับ path structure

| รูปแบบ | ตัวอย่าง |
|--------|---------|
| domain ตรงๆ | `/home/user/domain.com/wp-config.php` |
| public_html | `/home/user/public_html/wp-config.php` |
| domain ใน public_html | `/home/user/public_html/domain.com/wp-config.php` |
| home2, home3 ... | `/home2/user/domain.com/wp-config.php` |

## 💡 หมายเหตุ

- สคริปต์จะ **backup** ไฟล์เดิมทุกครั้งก่อนแก้ไข → `wp-config.php.bak_wpcron_YYYYMMDDHHMMSS`
- ถ้ามีบรรทัด `DISABLE_WP_CRON` ซ้ำ สคริปต์จะ**ลบซ้ำ**และเหลือแค่ 1 บรรทัดที่ถูกต้อง
- ถ้าแก้ไขล้มเหลว สคริปต์จะ**คืนค่า**จาก backup ให้อัตโนมัติ
