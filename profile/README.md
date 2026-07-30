<div align="center">

```
 █████╗ ███╗   ██╗██████╗
██╔══██╗████╗  ██║██╔══██╗
███████║██╔██╗ ██║██║  ██║
██╔══██║██║╚██╗██║██║  ██║
██║  ██║██║ ╚████║██████╔╝
╚═╝  ╚═╝╚═╝  ╚═══╝╚═════╝
```

**Anonim · Merkezi Olmayan · Yarı Sansürsüz**

*BIP-39 kimlik · libp2p ağ · terminal-native*

---

![Go](https://img.shields.io/badge/Go-1.22%2B-00ADD8?style=flat-square&logo=go&logoColor=white)
![libp2p](https://img.shields.io/badge/libp2p-0.34-7B2FBE?style=flat-square)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows-555555?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-22863a?style=flat-square)
![Status](https://img.shields.io/badge/durum-aktif%20geliştirme-orange?style=flat-square)

</div>

---

## AND Nedir?

AND, **sunucu olmadan** çalışan bir P2P terminal forum platformudur.  
Hesap oluşturmak yok. Kayıt yok. Merkezi bir altyapı yok.

Tek ihtiyacınız olan: terminal, `and` binary'si ve 12 kelimelik BIP-39 mnemonik.

```
$ ./and

  BIP-39 mnemonik (12 kelime):
  ▸ _

  →  12 kelimeyi gir ya da yenisini üret [n]
```

İlk girişte rastgele bir kimlik üretilir. Bu 12 kelime = sizin kimliğiniz. Kaybederseniz geri yolu yok.

---

---
## Temel Özellikler

| Özellik | Açıklama |
|---|---|
| **Sıfır Sunucu** | libp2p DHT + mDNS ile tam P2P keşif. Herhangi bir merkezî altyapıya bağımlılık yok. |
| **BIP-39 Kimlik** | 12 kelimelik mnemonik → Ed25519 anahtar çifti. Kimlik cüzdanda, sunucuda değil. |
| **GossipSub Forum** | Konular imzalanmış mesaj olarak yayınlanır; her düğüm lokal SQLite'a kopyalar. |
| **Moderasyon** | Kurucu Ed25519 imzası ile moderatör sertifikası yayınlar. Onaylar da imzalı. |
| **Özel Mesaj** | libp2p stream üzerinden uçtan uca şifreli DM. |
| **Otomatik Güncelleme** | GitHub Releases'tan imzalı binary çeker, yerinde günceller. |
| **Eklenti Sistemi** | XenForo tarzı: statik `plugin.json` manifest, aç/kapat, HTTP IPC API. |

---

## Mimari

```
┌─────────────────────────────────────────────────────┐
│                      and binary                     │
│                                                     │
│   ┌──────────┐   ┌──────────┐   ┌───────────────┐   │
│   │  crypto  │   │ network  │   │     forum     │   │
│   │ BIP-39   │   │ libp2p   │   │  SQLite store │   │
│   │ Ed25519  │   │ DHT/mDNS │   │  GossipSub    │   │
│   └──────────┘   └──────────┘   └───────────────┘   │
│                                                     │
│   ┌──────────┐   ┌──────────┐   ┌───────────────┐   │
│   │  dmmgr   │   │  modera- │   │   pluginapi   │   │
│   │  DM IPC  │   │  tion    │   │  HTTP server  │   │
│   └──────────┘   └──────────┘   └───────────────┘   │
│                                                     │
│   ┌─────────────────────────────────────────────┐   │
│   │                   tui                       │   │
│   │     Bubbletea · lipgloss · bubbles          │   │
│   └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
           │ AND_API_ADDR    │ tea.ExecProcess
           ▼                 ▼
    ┌─────────────┐   ┌─────────────┐
    │  and-plugin │   │  and-plugin │
    │   -admin    │   │  -moderator │  ... diğer eklentiler
    └─────────────┘   └─────────────┘
```

AND, eklentileri **ayrı process** olarak çalıştırır.  
Terminal kontrolü `tea.ExecProcess` ile devredilir; güvenli bir HTTP IPC kanalı üzerinden AND API'sine erişilir.

---

## Kurulum

### Hazır Binary (Önerilen)

GitHub Releases sayfasından işletim sisteminize uygun paketi indirin:

```
and-linux-amd64.tar.gz      → Linux x86-64
and-darwin-arm64.tar.gz     → macOS Apple Silicon
and-darwin-amd64.tar.gz     → macOS Intel
and-windows-amd64.zip       → Windows
```

Arşivi açın; `and` (veya `and.exe`) binary'sini PATH'inizdeki bir dizine kopyalayın.  
Eklentiler (`and-plugin-*.exe`) binary'nin yanında olmalıdır.

### Kaynaktan Derleme

```bash
git clone https://github.com/lucian95511/and
cd and
go build -o and ./cmd/and
go build -o and-plugin-admin    ./Eklentiler/admin
go build -o and-plugin-moderator ./Eklentiler/moderator
go build -o and-plugin-konu-ac  ./Eklentiler/konu_ac
go build -o and-plugin-ozel-chat ./Eklentiler/ozel_chat
```

---

## Eklenti Sistemi


### plugin.json — Statik Manifest

Her eklenti, binary'nin yanında bir `plugin.json` sidecar dosyası taşır.  
AND bu dosyayı okur; binary'yi çalıştırmadan menü bilgisini alır.

```json
{
  "name":        "ornek",
  "label":       "Örnek Eklenti",
  "version":     "1.0.0",
  "description": "Kısa açıklama",
  "author":      "Yazar Adı",
  "requires":    []
}
```

`"label": ""` → eklenti AND menüsünde görünmez (gizli / dahili eklenti).

### Aç / Kapat

AND TUI'inde Space tuşuyla her eklenti ayrıca açılıp kapatılabilir.  
Durum `AND_DATA_DIR/plugins_state.json` dosyasına kaydedilir.

### API Uç Noktaları

AND başlatıldığında rastgele bir `localhost` portunda HTTP API sunar.  
Adres `AND_API_ADDR` ortam değişkeniyle eklentiye iletilir.

```
GET  /identity          → kullanıcı adı, public key, peer ID
GET  /role              → IsFounder, IsModerator
GET  /pending           → onay bekleyen konular (mod/kurucu)
POST /approve           → {"post_id": "…"}
POST /reject            → {"post_id": "…"}
POST /approve-author    → {"author_key": "…"}
POST /create-post       → {"category","title","body","permanent_req"}
POST /send-dm           → {"peer_id","message"}
GET  /data-dir          → AND_DATA_DIR yolu
GET  /category          → AND_CATEGORY (gizli eklenti olarak açıldıysa)
```

### Eklentiler

## İşlevsel
* [foruma-konu-açma](https://github.com/AND-community/konu_ac) - Foruma konu açmak için gerekli.
* [Özel-chat](https://github.com/AND-community/ozel_chat) - Özel chat için gerekli.
* [DeltaX](https://github.com/AND-community/DeltaX) - Toplu proje geliştirmek için gerekli.

## İşlevsel
* [Pembis](https://github.com/AND-community/Pembis) - Pembe tema.

### Kendi Eklentinizi Yazın

```
Eklentiler/ornek/
├── main.go        ← tüm API özelliklerini gösteren referans implementasyon
└── plugin.json    ← statik manifest
```

`Eklentiler/ornek/main.go` dosyası şunları gösterir:

- `--manifest` bayrağı ile sidecar fallback
- `pluginapi.NewClientFromEnv()` ile API bağlantısı
- 7 ekranlı Bubbletea TUI (kimlik, bekleyen konular, konu oluşturma, DM, ayarlar)
- `textarea` + `textinput` + `viewport` kullanımı
- `safe()` ile ANSI injection koruması
- `pending` bayrağıyla çift-eylem engeli
- `AND_DATA_DIR` ile kalıcı ayar dosyası

<details>
<summary>Minimal eklenti şablonu (tıklayın)</summary>

```go
package main

import (
    "encoding/json"
    "fmt"
    "os"
    "github.com/lucian95511/and/internal/pluginapi"
)

var manifest = pluginapi.Manifest{
    Name:    "benim-eklentim",
    Label:   "Benim Eklentim",
    Version: "0.1.0",
    Author:  "Sen",
}

func main() {
    if len(os.Args) > 1 && os.Args[1] == "--manifest" {
        data, _ := json.Marshal(manifest)
        fmt.Println(string(data))
        return
    }

    client, err := pluginapi.NewClientFromEnv()
    if err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }

    id, _ := client.Identity()
    fmt.Println("Merhaba,", id.Name)
}
```

</details>

---

## Güvenlik Modeli

```
Kimlik Katmanı
  BIP-39 mnemonik → Ed25519 özel anahtar (yerel, şifreli)
  İmzasız mesaj ağda reddedilir.

Moderasyon Katmanı
  Kurucu, moderatör sertifikasını Ed25519 ile imzalar.
  Onay mesajları da imzalıdır; her düğüm bağımsız doğrular.
  Sunucu taraflı rol doğrulama — AND_API_ADDR bilen process
  bile rol olmadan /approve çağıramaz.

Terminal Güvenliği
  Ağdan gelen tüm string'ler ANSI escape kodu temizleme
  (safe()) filtresinden geçer. Injection mümkün değil.

Hassas Dosyalar (asla commit etme)
  identity.dat   — şifrelenmiş Ed25519 özel anahtar
  founder.key    — kurucu anahtar
  bans/          — moderasyon kayıtları
```

---

## Teknoloji Yığını

| Katman | Kütüphane |
|---|---|
| P2P Ağ | `go-libp2p` — DHT, mDNS, GossipSub, stream |
| TUI | `bubbletea` + `lipgloss` + `bubbles` |
| Kalıcı Depo | `mattn/go-sqlite3` |
| Kimlik | `tyler-smith/go-bip39` + `btcsuite/btcd/btcutil/hdkeychain` |
| Güncelleme | GitHub Releases API + `minio/selfupdate` |
| Test | `stretchr/testify` |

---

## Katkı

1. Repoyu fork edin
2. Feature branch oluşturun: `git checkout -b feat/özellik-adı`
3. Değişikliklerinizi commit edin
4. Pull request açın

Güvenlik açığı bildirmek için doğrudan iletişime geçin — issue açmayın.

---

<div align="center">

*AND tamamen Go ile yazılmıştır.*  
*Bir sunucuya güvenmek zorunda değilsiniz.*

</div>
