# Mihon Extensions TH

This public repository contains signed APKs and the Mihon index for seven Thai
extensions: Fin Manga, Kairew, Nekopost, ReadRealm, WhyToon, Up-Manga, and Speed Doujin.
Their source code and publish workflow are in the independent private repository
[`kaoitp/mihon-extensions-source`](https://github.com/kaoitp/mihon-extensions-source).

Add this URL in Mihon under Settings → Browse → Extension repos:

```text
https://raw.githubusercontent.com/oat018/mihon-extensions-th/main/index.pb
```

The existing `repo` branch URL also works. Both branches receive the same
index. The index lists only the seven new packages, while older APK files may
remain in `apk/` for archival purposes. These packages have new package names,
source IDs, and a new signing certificate. Previously installed extensions
will not update to them; install the new versions and migrate or re-add library
entries as needed.

The source repository's manual publish workflow builds signed APKs, verifies
their metadata and signing fingerprint, generates `index.pb`, `index.json`,
`index.min.json`, `repo.json`, and icons, then updates both branches atomically.
This project is not affiliated with Mihon or the content providers.
