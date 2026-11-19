# third_party — gömülü harici kaynaklar

## WhiteSur-kde

- **Kaynak:** https://github.com/vinceliuice/WhiteSur-kde
- **Sabitlenmiş commit:** `cf4df59ce91004f7ea39358b1b8ff917d5c329f7` (master, 2026-08-07)
- **Lisans:** GPL-3.0 (X-OS ile aynı — `WhiteSur-kde/LICENSE` korunur)
- **Neden repoda:** X-OS PlasmaX tabanının resmi teması (pencere dekoru
  aurorae, plasma global tema, Kvantum, renk şemaları, imleç, duvar kâğıdı).
  ISO derlemesi çevrimdışı/tekrarlanabilir olsun diye kaynak burada sabit.
- **Kullanım (ALI-22):** kurulum adımı `install.sh --dest` ile hedef dizine
  (canlı imajda `/usr/share` altına) kopyalar; SDDM teması `sddm/` altından.

## kde-material-you-colors (vendored DEĞİL)

~95 MB olduğu için repoda tutulmaz; ISO derlemesi sırasında
**sabitlenmiş commit'ten** codeload ile çekilir ve pip bağımlılıklarıyla
kurulur (PEP668: `--break-system-packages` veya venv — ALI-22'de karar).
Kaynak: https://github.com/luisbocanegra/kde-material-you-colors
