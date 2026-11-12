# Privacy X-Vault (XFolder) ↔ X-OS entegrasyon kontratı

**Durum:** kontrat sabitlendi; uygulama kodu henüz depoda değil.
Karar (2026-10-22): **önce kontrat + boş iskelet, kod sonra.**
Kullanıcı tarafı arayüz opsiyoneldir: servis yoksa hiçbir şey çökmez.

## D-Bus kimliği

| Alan | Değer |
|---|---|
| Servis | `io.xos.XVault` |
| Nesne | `/io/xos/XVault` |
| Arayüz | `io.xos.XVault` |
| Oturum | session bus |

## Zorunlu API (v1)

```
method  Status() → (s state, u unlocked)
        // state: "locked" | "unlocked"  · unlocked: 0 | 1

method  Unlock(s pin) → (b ok, s message)
        // Argon2id ile PIN doğrulama; başarısızlık sayacı uygulanır.
        // Not (v1): PIN session bus üzerinden gider; yerel oturumda kabul
        // edilebilir. Gelecek sürümde fd-tabanlı aktarım seçeneği düşünülür.

method  Lock() → ()
        // DEK bellekten silinir.

method  Encrypt(as paths) → (b ok, s message)
        // Kilitliyse false. Hedef: her dosya için .xvf kapsayıcı.

method  Decrypt(as paths, s target_dir) → (b ok, s message)
        // Çözülmüş dosyalar target_dir'e yazılır (varsayılan: aynı dizin).

method  SetPin(s old_pin, s new_pin) → (b ok, s message)
        // Yalnızca DEK yeniden sarılır — dosyalar tekrar şifrelenmez.

signal  StateChanged(s state)
signal  OperationFinished(s path, b ok, s message)
```

## Kripto tasarım (v1 — sabitlenecek kurallar)

| Öğe | Karar |
|---|---|
| Şifreleme | **AES-256-GCM** — 32B anahtar, **96-bit nonce**, 128-bit tag |
| AAD | Kapsayıcı başlığı (magic, sürüm, KDF param.) — başlık değişirse tag kırılır |
| PIN → anahtar | **Argon2id** (önerilen varsayılan: m=64 MiB, t=3, p=4) → KEK |
| Anahtar ağacı | Rastgele **DEK** (32B) bir kez üretilir; KEK ile **sargılanır** (LUKS keyslot mantığı) → **PIN değişince dosyalar yeniden şifrelenmez** |
| Nonce | Dosya başına **benzersiz** rastgele 96-bit — **asla tekrar kullanılmaz** (GCM'de tekrar felakettir) |
| Kapsayıcı | `XVF1` magic ‖ sürüm ‖ KDF params ‖ salt ‖ wrapped-DEK ‖ nonce ‖ tag ‖ ciphertext |
| Bellek | Anahtar tamponları `mlock` + kullanımdan sonra sıfırlama |
| Erişim | `sudo` **gerekmez**; sistem geneli politika gerekirse **polkit** (`polkitd` imajda) |
| Faz 1.5 | Opsiyonel **keyfile** (PIN + keyfile = 2 faktör) |

## Fazlar

1. **Faz 1 (ŞU AN):** kontrat + boş iskelet — bu dosya.
2. **Faz 2:** CLI (`xos-xvault`) + küçük pencere (PIN diyalog) + **Thunar custom
   action** ("X-Vault'a şifrele / çöz") + `.xvf` MIME (çift tık → PIN → çöz).
3. **Faz 3:** Hypervisor Faz 2 ile — DEK yalnızca "vault-VM" RAM'inde tutulur,
   anahtar host'a hiç yazılmaz.

## Örnek implementasyon iskeleti (kabul testi)

```python
# X-Vault servisi (dbus-python / sdbusii — dilediğinle)
bus = SessionBus()
bus.request_name("io.xos.XVault")

class XVault(ServiceObject):
    def __init__(self):
        self.dek = None  # yalnızca unlock sonrası bellekte

    @method()
    def Status(self) -> "su":
        return ("unlocked" if self.dek else "locked",
                1 if self.dek else 0)

    @method()
    def Unlock(self, pin: "s") -> "bs":
        # argon2id(pin, salt) → KEK → wrapped-DEK çöz → self.dek
        return True, "ok"

    @method()
    def Encrypt(self, paths: "as") -> "bs":
        # her yol için: dek ile AES-256-GCM, benzersiz nonce → .xvf
        return True, "ok"
```

## Uyumluluk kuralı

X-Defender ile aynı: yeni sinyal/metot **eklenir**, mevcut imza **asla**
değiştirilmez; kırıcı değişiklik `io.xos.XVault2` adıyla yayınlanır.
