# Mihon Extensions TH

repo นี้เป็นพื้นที่ **publish** สำหรับส่วนขยาย Mihon ภาษาไทย โดยจัดโครงสร้างตาม Keiyoushi: ซอร์สและ CI อยู่ใน fork ของ `extensions-source`; สาขา `repo` นี้เก็บเฉพาะ index, icon และ APK ที่เซ็นแล้ว

## เพิ่ม repo ใน Mihon

Mihon → Settings → Browse → Extension repos → Add แล้ววาง URL นี้:

```
https://raw.githubusercontent.com/oat018/mihon-extensions-th/repo/index.pb
```

`index.pb` เป็น protobuf index รุ่นปัจจุบันที่ Mihon ใช้ ส่วน `index.min.json` เก็บไว้เพื่อรองรับแอปรุ่นเก่าเท่านั้น

## ไฟล์ที่ publish

- `index.pb` — index ที่ Mihon อ่าน
- `index.json` — index เดียวกันในรูปแบบอ่านได้
- `repo.json` — descriptor พร้อม fingerprint ของ signing key
- `index.min.json` — legacy index
- `apk/` — APK ที่ publish
- `icon/` — icon ที่ index อ้างถึง

APK ทั้งหมดใน repo ต้องใช้ signing certificate เดียวกัน ตัวสร้าง index จะหยุดทันทีหากพบหลายลายเซ็น

## การพัฒนาและ publish

ซอร์สอยู่ใน fork `kaoitp/extensions-source` บนสาขา `main` และติดตาม upstream `keiyoushi/extensions-source` การ push การเปลี่ยนแปลง Kairew จะเรียก `.github/workflows/publish-th.yml` ซึ่งทำตามลำดับนี้:

1. build release APK และ JAR ด้วย signing key จาก GitHub Actions secrets
2. checkout `oat018/mihon-extensions-th` สาขา `repo`
3. สร้าง `index.pb`, `index.json`, `repo.json`, legacy index และ icon
4. commit และ push เฉพาะผลลัพธ์ที่ publish

Secrets ที่ source repo ต้องมี: `SIGNING_KEY`, `ALIAS`, `KEY_STORE_PASSWORD`, `KEY_PASSWORD`, และ `PUBLISH_SSH_KEY` ซึ่งเป็น deploy key ที่มีสิทธิ์เขียนเฉพาะ publish repo

โครงการนี้ไม่เกี่ยวข้องกับ Mihon หรือผู้ให้บริการเนื้อหาอย่างเป็นทางการ
