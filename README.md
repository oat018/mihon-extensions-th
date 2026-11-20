# Mihon Extensions TH

This public repository contains signed APKs, JVM JARs, and the Mihon index for three Thai
extensions: Kairew, Up-Manga, and Speed Doujin.
Their source code and publish workflow are in the independent private repository
[`kaoitp/mihon-extensions-source`](https://github.com/kaoitp/mihon-extensions-source).

Add this URL in Mihon under Settings → Browse → Extension repos:

```text
https://raw.githubusercontent.com/oat018/mihon-extensions-th/main/index.pb
```

The existing `repo` branch URL also works. Both branches receive the same
index. The index lists the active packages, while older APK files may
remain in `apk/` for archival purposes. These packages have new package names,
source IDs, and a new signing certificate. Previously installed extensions
will not update to them; install the new versions and migrate or re-add library
entries as needed.

Nekopost, ReadRealm, WhyToon, and 1668Manga are available from
[Keiyoushi](https://github.com/keiyoushi/extensions). Its extensions have
different source IDs. Install them and migrate any library entries in Mihon.

The source repository's manual publish workflow builds signed APKs and JARs, verifies
their metadata and signing fingerprint, generates `index.pb`, `index.json`,
`index.min.json`, `repo.json`, and icons, then updates both branches atomically.
This project is not affiliated with Mihon or the content providers.
