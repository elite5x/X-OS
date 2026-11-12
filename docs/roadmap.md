# X-OS Yol Haritası

Sürüm adayları Debian Stable döngüsüne bağlanır; hedef her aşamada
`make lint && make test` + yeşil CI.

## v0.1 "İskele" — şu an

- [x] Token hattı (JSON → QML/CSS, CI drift koruması)
- [x] Liquid Glass malzemesi (`GlassSurface`) + bileşen seti
- [x] Shell yüzeyleri: TopBar, Dock (büyüme efektli), Launcher (arama +
      sticker düzenleme), ControlCenter, NotificationCenter, LockScreen
- [x] C++ çekirdek: `xos.api` / `xos.apps` / `xos.system` / `xos.settings`
- [x] X-Defender D-Bus köprüsü (opsiyonel, yoksa gizli)
- [x] live-build ISO altyapısı + `xos-session` + SDDM
- [x] CI: lint + test + Qt6 derleme (Debian trixie container)
- [x] ISO pipeline CI'da artifact olarak dönüyor (run `39764563047`,
      artifact `xos-live-iso` ≈2,45 GB; shell binary'si + ikon/imleç temaları
      imaja gömülü, paket listesi ön uçuşlu)

## Yön kararları (2026-10-22)

- [x] **PlasmaX tabanı:** sıfırdan kabuk yerine **KDE Plasma 6** + **WhiteSur
      KDE** teması + **kde-material-you-colors** (duvar kâğıdından Material You
      3 paleti) — X-OS kimliği marka katmanı olarak (duvar kâğıdı, ikonlar,
      logo, kilit ekranı, kendi uygulamalar). v0.1 QML bileşenleri ileride
      panel applet'i/tema olarak taşınır.
- [x] **X-Vault (Privacy XFolder):** önce kontrat + boş iskelet →
      `integrations/x-vault/` + `apps/xvault/` (AES-256-GCM + PIN/Argon2id,
      Thunar yanına Debian paketi; Faz 2 kod).
- [x] **Hypervisor:** önce yalnızca kontrat → `integrations/hypervisor/`
      (`io.xos.Hypervisor`, system bus + polkit; KVM/libvirt yönetimi Faz 2;
      ilk müşterisi X-Vault Faz 3 = DEK vault-VM izolasyonu).

## Yeni milestone: "PlasmaX tabanı" (bunlar bitince v0.2'ye girilir)

- [ ] ISO paket listesi: `plasma-workspace` + sddm (mevcut) + WhiteSur KDE +
      `kde-material-you-colors` kurulumu
- [ ] `xos-session` → plasmashell oturumu (KWin duruyor, plasmex yerine)
- [ ] Marka katmanı: X-OS duvar kâğıdı, ikon seti, kilit ekranı, ayarlar
- [ ] Material You: duvar kâğıdı değişince palet canlı güncellensin
- [ ] v0.1 shell bileşenlerinin taşınması değerlendirmesi (applet/tema)

## v0.2 "Masaüstü"

- [ ] Masaüstü ikonları (`.desktop` sürükle-bırak, ızgara snap)
- [ ] KWin scripti: aktif pencere adı, tam ekran takibi
- [ ] Duvar kâğıdı seçici (jetonlanmış: aurora / ember / light / görsel)
- [ ] Sticker paleti genişlemesi (yazı sticker'ı, saatin sticker'ı)
- [ ] Bildirim daemon köprüsü (`org.freedesktop.Notifications` sahiplenme)
- [ ] Tema geçişi animasyonlu (metin/arka plan lerp)

## v0.3 "Kurulum"

- [ ] Calamares kurulum programı (X-OS bölümü, kullanıcı, gruplar)
- [ ] `live-build` bootstrap köprüsü → GitHub Actions artifact ISO
- [ ] İlk boot sihirbazı (dil, klavye, kullanıcı, güncelleme denetimi)
- [ ] OTA güncelleme denetleyicisi (apt + xos-release kanalları)

## v0.4 "Sağlam ses"

- [ ] Ekran kilidi için `pam_xos` (demo → gerçek PAM)
- [ ] PipeWire ses mikseri paneli (cihaz seçici, uygulama akışları)
- [ ] Ağaç: Wi-Fi seçici NM köprüsü (şimdilik etiket var)
- [ ] Erişilebilirlik geçişi: okuma sırası, kontrast çifti, hareket azaltma

## Gizlilik & altyapı (kontratlar hazır — kod sırasıyla)

- [x] X-Vault D-Bus kontratı (`io.xos.XVault`) + kripto tasarımı
- [x] Hypervisor D-Bus kontratı (`io.xos.Hypervisor`) + polkit notu
- [ ] X-Vault Faz 2: CLI + PIN penceresi + Thunar aksiyonu + `.xvf` MIME
- [ ] X-Vault Faz 3: vault-VM izolasyonu (Hypervisor Faz 2 ile)
- [ ] Hypervisor Faz 2: libvirt/KVM yönetimi + `io.xos.hypervisor.manage`

## v0.5 "Vitrin"

- [ ] KWin Liquid Glass efekt plugin'i (pencere bulanıklığı, köşe kırılması)
- [ ] Animasyon kütüphanesi: spring easing yardımcıları, sekme geçişleri
- [ ] Uygulama vitrini: X-Defender entegrasyonu (tehdit bildirimleri)
- [ ] Performans: shader bütçesi, 60/120 Hz ölçüm süiti

## Açık kaynak / topluluk

- [ ] CONTRIBUTING.md (lint/test kapısı, token kuralları)
- [ ] Issue şablonları (bug / tasarım / ISO)
- [ ] İlk dış katkıcı denemesi: `design/preview` üzerinden tasarım incelemesi
