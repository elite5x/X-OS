# X-OS Hypervisor ↔ X-OS entegrasyon kontratı

**Durum:** kontrat sabitlendi; KVM/libirt yönetimi **Faz 2** — bu depoda kod yok.
Karar (2026-10-22): **şimdilik yalnızca kontrat.**
Servis **opsiyoneldir**: bulunamazsa istemci "yok" der, uygulamalar sessizce devam eder.

## D-Bus kimliği

| Alan | Değer |
|---|---|
| Servis | `io.xos.Hypervisor` |
| Nesne | `/io/xos/Hypervisor` |
| Arayüz | `io.xos.Hypervisor` |
| Oturum | **system bus** (yetki gerekir — aşağıdaki polkit notu) |

> Not: VM yönetimi ayrıcalık ister; bu servis **system bus**'ta yaşar ve
> `io.xos.hypervisor.manage` polkit aksiyonu ister (imajda `polkitd` var).
> X-Defender/XVault oturum bus'ında kalır.

## Zorunlu API (v1)

```
method  Status() → (s state, s engine)
        // state: "available" | "unavailable"
        // engine: "kvm" (gelekte qemu/lxc) — yoksa ""

method  ListMachines() → (a(sss) machines)
        // (name, state, image)  · state: "running" | "stopped" | "paused"

method  CreateMachine(s name, s image_path) → (b ok, s message)
method  StartMachine(s name) → (b ok, s message)
method  StopMachine(s name) → (b ok, s message)
method  DestroyMachine(s name) → (b ok, s message)
        // DestroyMachine veri kaybı riskine karşı polkit ile korunur.

signal  MachineStateChanged(s name, s state)
```

## Mimari notlar (Faz 2 için)

- **Motor:** KVM + `qemu-system-x86` + `libvirt-daemon-system` (Debian paketleri
  hazırdır); görüntü dosyaları `/var/lib/xos/vms/` altında.
- **Gereksinim:** gerçek donanımda VT-x/AMD-V; VirtualBox test ortamında
  **nested virtualization** açık olmalıdır.
- **Tehdit modeli dürüstlüğü:** hypervisor userland sızıntılarına karşı güçlü
  izolasyon verir; host kernel/root sızıntısına karşı son savunma hattı **değildir**.
- **Ana kullanım:** kritik süreçlerin izole guest'te koşulması — ilk müşterisi
  **X-Vault Faz 3**'tür (DEK yalnızca vault-VM'nin RAM'inde).
- Hafif alternatif (aynı hedef, daha az izolasyon): AppArmor/seccomp sandbox
  veya `systemd-nspawn` — karar Faz 2'de verilir.

## Örnek implementasyon iskeleti (kabul testi)

```python
# Hypervisor servisi (system bus + polkit)
bus = SystemBus()
bus.request_name("io.xos.Hypervisor")

class Hypervisor(ServiceObject):
    @method()
    def Status(self) -> "ss":
        return ("available" if shutil.which("virsh") else "unavailable", "kvm")

    @method()
    def ListMachines(self) -> "a(sss)":
        # virsh list --all --parseable → [(name, state, image), ...]
        return []

    @method()
    def StartMachine(self, name: "s") -> "bs":
        # polkit check: io.xos.hypervisor.manage
        return True, "ok"
```

## Uyumluluk kuralı

Yeni sinyal/metot eklenir, mevcut imzalar değişmez; kırıcı sürüm
`io.xos.Hypervisor2` adıyla yayınlanır.
