# X-Vault (Privacy XFolder) — uygulama iskeleti

**Durum:** boş iskelet (2026-10-22 kararı: önce kontrat, kod sonra).
Kontrat: [`integrations/x-vault/README.md`](../../integrations/x-vault/README.md).

## Ne yapılacak (özet)

Kullanıcı dosyalarını **AES-256-GCM** ile şifreleyen, erişim için **PIN**
(Argon2id) isteyen gizlilik kasası. Debian paketi `xos-xvault` olarak
Thunar'ın yanında menüde durur.

## Gelecek dizin düzeni

```
apps/xvault/
├── README.md            (bu dosya)
├── src/                 UI + mantık (Faz 2)
├── service/             io.xos.XVault servisi (Faz 2)
├── cli/                 xos-xvault (Faz 2, script/Thunar aksiyonları)
└── packaging/           debian/ (Faz 2)
```

## Fazlar

1. **Faz 1 (ŞU AN):** kontrat + bu iskelet.
2. **Faz 2:** CLI + PIN penceresi + Thunar custom action + `.xvf` MIME.
3. **Faz 3:** vault-VM izolasyonu (Hypervisor Faz 2 ile).
