# mihon-extensions-th

ส่วนขยาย (extension) ภาษาไทยสำหรับ [Mihon](https://mihon.app) — สร้างเองจากลิสต์เว็บอ่านการ์ตูนไทย
repo นี้เก็บเฉพาะ **ผลลัพธ์ที่ build แล้ว**: `index.min.json` + APK ใน `apk/`

> ⚠️ **ยังไม่มีตัวไหนถูกทดสอบบนเครื่องจริง** — build ผ่าน + lint ผ่าน + verify กับ HTML/endpoint จริงเท่านั้น
> จุดที่เสี่ยงที่สุดคือการดึงรูปหน้าการ์ตูน (`getPageList`) ของ WhyToon กับ ReadRealm ดูหัวข้อ [ค้างอยู่](#ค้างอยู่)

## มีอะไรบ้าง

| ส่วนขยาย | เวอร์ชัน | baseUrl | สถานะ |
|---|---|---|---|
| WhyToon | 1.6.1 (`versionCode 1`) | https://whytoon.com | เขียนใหม่ทั้งตัว |
| ReadRealm | 1.6.1 (`versionCode 1`) | https://readrealm.co | เขียนใหม่ทั้งตัว เฉพาะฝั่ง `/comics` (ไม่รวมนิยาย) |
| Nekopost | 1.6.16 (`versionCode 16`) | https://www.nekopost.net | ของ Keiyoushi ที่แพตช์บั๊ก pagination |

APK เป็น **self-signed ด้วย debug key** (ไม่มี `signingkey.jks` ตอน build) ตอนติดตั้งเครื่องจะขึ้น
"Untrusted extension" ให้กดยอมรับใน Mihon → Settings → Browse → Extensions

## วิธีติดตั้ง

repo นี้เป็น **private** — Mihon ดึง `index.min.json` ผ่าน raw URL แบบไม่ล็อกอิน เพราะฉะนั้น
**เพิ่มเป็น extension repo ในแอปไม่ได้** ต้องโหลด APK จาก `apk/` แล้ว sideload เอง

ถ้าอยากให้แอปดึงเองได้ ต้องเปลี่ยนเป็น public ก่อน:

```bash
gh repo edit oat018/mihon-extensions-th --visibility public
```

แล้วเพิ่ม repo URL ในแอปเป็น raw URL ของ `index.min.json` บน branch `main`

## ซอร์สโค้ดอยู่ไหน

**ไม่ได้อยู่ใน repo นี้** โค้ด Kotlin อยู่ในเครื่องที่ `Desktop/mihon/extensions-source`
ซึ่งเป็น shallow clone ของ [keiyoushi/extensions-source](https://github.com/keiyoushi/extensions-source)
และ **ยังไม่ได้ commit** ถ้าจะทำต่อ/ส่ง PR ควร fork upstream แล้วย้ายโฟลเดอร์เหล่านี้เข้าไป:

- `src/th/whytoon/` (ใหม่)
- `src/th/readrealm/` (ใหม่)
- `src/th/nekopost/` (แก้ 2 ไฟล์ ดูด้านล่าง)

### แพตช์ Nekopost คืออะไร

`parseProjectList` คำนวณ `hasNextPage` จากลิสต์ที่ **กรองแล้ว** แต่ API คืนค่าคละ `projectType`
(ได้ manga ประมาณ 45–48 จาก 100 รายการ) ผลคือหน้าเต็มแต่มี manga ไม่ถึง `SEARCH_PAGE_SIZE`
→ แอปคิดว่าจบแล้ว → **search ไม่เคยโหลดหน้า 2** แก้โดยนับจาก response ดิบแทน (`versionCode` 15 → 16)

## วิธี build

```bash
export JAVA_HOME="/c/Program Files/Eclipse Adoptium/jdk-17.0.20.101-hotspot"   # JDK 17
./gradlew :src:th:<name>:assembleRelease :src:th:<name>:lintRelease
```

- Android SDK ที่ `~/Android/Sdk` (ชี้ผ่าน `local.properties`), ต้องมี platform **android-37** + build-tools 37.0.0 (compileSdk 37)
- source ใหม่ extend `KeiSource` (libVersion 1.6) — metadata ใส่ใน `build.gradle.kts` บล็อก `source {}` ผ่าน KSP **ห้าม** override ในคลาส Kotlin
- scaffold ตัวใหม่: `python ext-bootstrap.py -n Name -l th -u https://... -c MIXED`
- อ่าน `CONTRIBUTING.md` ของ upstream ก่อนเขียน — convention เปลี่ยนไปจาก Tachiyomi เดิมเยอะ

**กับดัก:** อย่า pipe gradle ผ่าน `grep`/`tail` เพื่อเช็คว่าสำเร็จไหม มันกลืน exit code
(เคยเจอ exit 0 ทั้งที่ build พัง) ให้เขียนลง log file แล้วเช็ค `$?` แยก

## ค้างอยู่

1. **เทสบนเครื่องจริง** — ยังไม่เคยรันเลยสักตัว จุดที่ยังไม่ verify คือ:
   - `Whytoon.kt:133` `getPageList` — ดึง `data.images` จาก Next.js flight payload
   - `Readrealm.kt:127` `getPageList` — ดึง `chapter_content` จาก Next.js flight payload

   ทั้งคู่หา payload ด้วย `extractNextJs { ... }` ที่ match key ตามชื่อ ถ้าเว็บเปลี่ยนรูป payload
   จะ error ว่า "Unable to find the page list" ส่วนอื่น (popular/latest/search/details/chapters)
   verify กับ HTML และ endpoint จริงแล้ว

2. **kairew.com — map ครบแล้ว แต่ติด login** เขียนต่อได้เลยถ้ามีบัญชี:
   - หน้าเว็บเป็น Laravel + Inertia + Vue และ **ฝัง route table ของ Ziggy ไว้ใน HTML** (`"routes":{...}`, 303 routes)
     → หา endpoint ด้วยการ grep HTML อย่าเดา URL (SPA catch-all คืน 200 ทุก path, status code บอกอะไรไม่ได้)
   - listing: `POST /api/home-data` (ต้องมี `X-CSRF-TOKEN` จาก Inertia prop `csrf_token` + session cookie)
   - search: `GET /api/search/books/all?keyword=`
   - details: Inertia GET `/cartoon/<uploader>/<id>-<slug>` → prop `book_data`
   - chapters: `GET /api/books/<id>/episodes?offset=0&sort=first` (50/หน้า)
   - reader: `/{type}/book/{book_id}/read/{episode_id}` → component `Member/Book/Read`
   - **ตัวบล็อก:** `episode.content` เป็น `null` ถ้าไม่ล็อกอิน (ตอนที่ตัวมี `price_type: "free"` ก็ตาม),
     route `member.` redirect 302 ไปหน้า login → ต้องทำเป็น extension แบบมี login preference
   - หมายเหตุ: หน้าเว็บ kairew ทำ Chrome automation ค้างสองครั้ง (`devtools-protection.js`) ใช้ curl จะง่ายกว่า

3. **readtoon.com — จงใจไม่ทำ** reader ไม่ได้ส่ง URL รูปมาตรง ๆ แต่ผ่าน AES หลายชั้น
   พร้อม bot detection (turnstile + server-side bot flag) การทำ extension ให้ = การถอดกลไกป้องกัน
   เนื้อหาของเว็บ จึงไม่ทำ ไม่ใช่เพราะทำไม่ได้ ถ้าจะทำต่อก็ควรรู้ตรงนี้ไว้

4. **fin-manga.com** — Keiyoushi มี extension ให้อยู่แล้ว ไม่ต้องเขียน
