# GTS OTA-Poll — HIL Demo/Test Host

> **Ne bu depo:** GTS_Open **Epic 36 (OTA-poll — Tuya-BAĞIMSIZ kendi-server yoklama)** için **HIL test/demo host**'u.
> Cihazın kendi server'ından firmware yoklayıp (poll) indirmesini/uygulamasını **gerçek board üzerinde** kanıtlamak için kullanılır.
> `raw.githubusercontent.com` = geçerli-HTTPS OTA-endpoint (cihaz `gts_ota` crt-bundle şart koşar; cloudflared-tünel GEREKMEZ).
>
> 🔴 **Production DEĞİL — yalnız test artefaktı.** Sahaya çıkan cihaz gerçek fleet-server'a yönlendirilir, bu depoya değil.

## İçerik

| Dosya | Ne |
|---|---|
| `manifest.txt` | GTSFW1 manifest (`version` + `image_url`); cihaz `gts_ota_manifest_parse` ile okur. `compatible_hw`/`min_version` YOK → F1-1 gate SKIP → yalnız sürüm-kıyas. |
| `gts_open_prod_signed.bin` | İmzalı **production** app image (RSA, `C:/gts-secrets` anahtarı; `espsecure verify` OK). Paired Tuya-board OTA-hedefi. |
| `gts_solar_controller.bin` | **Standalone** build image (standalone board testi için). |

## Nasıl kullanılır (HIL poll)

1. Board'un poll-URL'ini bu manifest'e yönlendir:
   - Kconfig: `CONFIG_GTS_OTA_MANIFEST_URL="https://raw.githubusercontent.com/mamixsystem/gts-ota-demo/main/manifest.txt"`
   - veya NVS: namespace `gts_ota` / key `poll_url`
2. Board, poll-timer'ında (boot-grace sonrası) manifest'i çeker → sürüm-kıyas → `SHOULD_UPDATE` → (`auto_apply` veya Story-B onayı ile) `image_url`'deki imzalı image'i indirir + doğrular + uygular.
3. Manuel tetik (alternatif): seri konsol `{"cmd":"ota","url":"<image_url>"}`.

## Bağlam / kanıt (GTS_Open Epic 36)

- **Story A** device-side poll · **B** bildirim/onay UX · **C** production imzalı-build · **D** `tuya_ota_start` nötrle.
- **Fleet-OTA fix** (GTS_Open commit `fbd6df1f`): `gts_ota_start_gated` ağ-kapısı `gts_wifi_is_connected()` → `esp_netif` netif-IP; Tuya-yönetimli WiFi'de OTA-apply'ı açar (eskiden fleet'te HEP bloklanıyordu).
- **2026-07-05 HW-KANIT:** paired Tuya-board'da bu host'tan uçtan-uca OTA — `downloading → verifying → rebooting`, `factory → ota_0` partition-switch, RSA-imza doğrulandı, **Tuya pairing korundu**.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
