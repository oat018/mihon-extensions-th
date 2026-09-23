# Mihon Extensions TH

สาขา `main` ของ `oat018/mihon-extensions-th` เก็บไฟล์สำหรับติดตั้งส่วนขยายภาษาไทยใน Mihon: ดัชนี ไอคอน และ APK ที่เซ็นแล้ว ซอร์สของ Kairew กับ WhyToon และระบบ build/publish อยู่ที่ [kaoitp/extensions-source](https://github.com/kaoitp/extensions-source)

## เพิ่ม repo ใน Mihon

Mihon → Settings → Browse → Extension repos → Add แล้ววาง URL นี้:

```
https://raw.githubusercontent.com/oat018/mihon-extensions-th/main/index.pb
```

`index.pb` เป็น protobuf index รุ่นปัจจุบันที่ Mihon ใช้ ส่วน `index.min.json` เก็บไว้เพื่อรองรับแอปรุ่นเก่าเท่านั้น

URL เดิมที่ชี้สาขา `repo` ยังใช้งานได้ โดย workflow ซิงก์สาขานั้นเพื่อรองรับผู้ที่เพิ่ม repo ไว้ก่อนหน้านี้

## ไฟล์ที่ publish

- `index.pb` — index ที่ Mihon อ่าน
- `index.json` — index เดียวกันในรูปแบบอ่านได้
- `repo.json` — descriptor พร้อม fingerprint ของ signing key
- `index.min.json` — legacy index
- `apk/` — APK ที่ publish
- `icon/` — icon ที่ index อ้างถึง

ตอนนี้มี Kairew, Nekopost, ReadRealm และ WhyToon โดยซอร์สของ Kairew กับ WhyToon อยู่ใน source fork ส่วน Nekopost และ ReadRealm เก็บเป็น APK ใน repo นี้ APK ทั้งหมดใช้ signing certificate เดียวกัน และตัวสร้าง index จะหยุดหากพบหลายลายเซ็น ผู้ที่ติดตั้ง Nekopost, ReadRealm หรือ WhyToon จากชุดเก่าซึ่งใช้ลายเซ็นอื่น ต้องถอนการติดตั้งตัวเก่าก่อนติดตั้งจาก repo นี้

## การพัฒนาและ publish

ซอร์สอยู่ใน fork `kaoitp/extensions-source` บนสาขา `main` และติดตาม upstream `keiyoushi/extensions-source` การ push การเปลี่ยนแปลง Kairew หรือ WhyToon จะเรียก [Publish Thai extensions](https://github.com/kaoitp/extensions-source/blob/main/.github/workflows/publish-th.yml) ซึ่งทำตามลำดับนี้:

1. build release APK ของ Kairew และ WhyToon ด้วย signing key จาก GitHub Actions secrets
2. checkout `oat018/mihon-extensions-th` สาขา `main`
3. สร้าง `index.pb`, `index.json`, `repo.json`, legacy index และ icon
4. commit ผลลัพธ์ที่ publish ลง `main` และซิงก์ `repo` เพื่อรองรับ URL เดิม

source repo เก็บ signing key และ private deploy key ใน GitHub Actions secrets ส่วน public deploy key ต้องได้รับสิทธิ์เขียนที่ publish repo ก่อน workflow จะ push ได้

โครงการนี้ไม่เกี่ยวข้องกับ Mihon หรือผู้ให้บริการเนื้อหาอย่างเป็นทางการ
