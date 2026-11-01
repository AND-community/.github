<div align="center">

```
 █████╗ ███╗   ██╗██████╗
██╔══██╗████╗  ██║██╔══██╗
███████║██╔██╗ ██║██║  ██║
██╔══██║██║╚██╗██║██║  ██║
██║  ██║██║ ╚████║██████╔╝
╚═╝  ╚═╝╚═╝  ╚═══╝╚═════╝
```

**Takma Adlı · Merkezi Olmayan · Yarı Sansürsüz**

*Ed25519 kimlik · libp2p ağ · terminal-native*

[**and-community.github.io**](https://and-community.github.io/tr/)

[English](https://github.com/AND-community/.github/blob/main/profile/README.md) · **Türkçe**

---

![Go](https://img.shields.io/badge/Go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white)
![libp2p](https://img.shields.io/badge/a%C4%9F-libp2p-7B2FBE?style=flat-square)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows-555555?style=flat-square)
![Lisans](https://img.shields.io/badge/lisans-AGPL--3.0-22863a?style=flat-square)
![Durum](https://img.shields.io/badge/durum-aktif%20geli%C5%9Ftirme-orange?style=flat-square)

</div>

---

## AND Nedir?

AND, **sunucu olmadan** çalışan, terminalde açılan eşten eşe (P2P) bir forum topluluğudur.  
Hesap oluşturmak yok. Kayıt yok. Merkezi bir altyapı yok.

Tek ihtiyacın: terminal, `and` binary'si ve bir parola. İlk açılışta AND kimliğini — bir Ed25519
anahtar çifti — üretir ve sana bir kez 32 karakterlik bir **kurtarma kodu** olarak gösterir. Bu kod
senin kimliğindir: onunla kimliğini başka bir bilgisayarda ya da parolanı unuttuğunda geri
getirirsin; kaybedersen geri yolu yok.

Her düğüm hem istemci hem sunucudur. Konular, yanıtlar ve sohbet mesajları imzalanıp libp2p
GossipSub üzerinden yayılır; her düğüm kendi kopyasını yerel bir SQLite veritabanında tutar. Arayüz
altı dildedir: Türkçe, English, Deutsch, Español, Français, Русский.

---

## Temel Özellikler

| Özellik | Açıklama |
|---|---|
| **Sıfır sunucu** | libp2p DHT + mDNS ile eş keşfi; herhangi bir merkezi altyapıya bağımlılık yok |
| **Anahtar tabanlı kimlik** | 128 bit entropi (12 kelimelik BIP-39 anımsatıcısına eşdeğer) → Ed25519 anahtar çifti; kendi cihazında parolanla şifreli saklanır |
| **İmzalı forum** | 34 kategoride konular, imzalı GossipSub mesajı olarak yayınlanır; her düğüm doğrulayıp saklar. Konular 5 gün sonra kendiliğinden silinir |
| **Genel sohbet** | Hız sınırlı, geçmişi senkronlanan, imzalı bir P2P sohbet kanalı |
| **Özel mesaj ve dosya** | İki eş arasında doğrudan libp2p akışı; alıcının onayıyla, kaldığı yerden devam edebilen dosya aktarımı |
| **Kişisel site** | Her kullanıcı küçük, çok sayfalı bir site yayınlayabilir; forum gibi imzalı ve senkronlanır |
| **Moderasyon** | Kurucu imzalı moderatör sertifikaları yayınlar; banlar (1 saatten kalıcıya), konuları erken silme ve denetim geçmişi |
| **Eklentiler** | AND'la yerel bir HTTP API üzerinden konuşan bağımsız binary'ler; her birinin kendi anahtarı ve kullanıcının onayladığı izinleri vardır |
| **Otomatik güncelleme** | GitHub Releases'tan; yalnızca özet dosyası projenin Ed25519 imzasını taşıyorsa uygulanır |

---

## Depolar

| Depo | Ne |
|---|---|
| [**AND**](https://github.com/AND-community/AND) | Uygulamanın kendisi; ayrıca Yönetici Paneli, Özel Chat, Site ve Örnek eklentileri ile VS Code uzantısı (`and-vscode`) |
| [**konu_ac**](https://github.com/AND-community/konu_ac) | Konu açma eklentisi — forumda konu açmak için gerekli |
| [**DeltaX**](https://github.com/AND-community/DeltaX) | Kendi sürüm kontrol sistemiyle özel gruplar; üyeler arasında uçtan uca şifreli senkron |
| [**Pembis**](https://github.com/AND-community/Pembis) | AND'ı pembeye boyayan tema eklentisi |
| [**and-arama-motoru**](https://github.com/AND-community/and-arama-motoru) | AND Arama Motoru: ağdaki içeriği indeksleyip terminalden aratan, bağımsız ve salt-okunur bir eş |
| [**and-community.github.io**](https://github.com/AND-community/and-community.github.io) | [and-community.github.io](https://and-community.github.io/tr/) adresindeki İngilizce ve Türkçe web sitesi |
| [ozel_chat](https://github.com/AND-community/ozel_chat) | *Arşivlendi.* Özel Chat artık AND deposunun içinde (`Eklentiler/ozel_chat`) |

---

## Başlarken

Kaynaktan derlemek için Go 1.26.4 ya da üzeri gerekir (1.21'den sonraki eski Go sürümleri doğru
araç zincirini kendisi indirir):

```bash
git clone https://github.com/AND-community/AND.git
cd AND
go build -o and ./cmd/and

# Eklentiler kendi klasörlerine, plugin.json'ın yanına derlenir
go build -o Eklentiler/ozel_chat/and-plugin-ozel_chat ./Eklentiler/ozel_chat

# Konu Aç (konu açmak için gerekli) ayrı bir depodadır
git clone https://github.com/AND-community/konu_ac.git Eklentiler/konu_ac
go build -o Eklentiler/konu_ac/and-plugin-konu_ac ./Eklentiler/konu_ac

./and
```

Windows'ta binary adlarına `.exe` ekle. Bütün eklentiler, ldflags ve kurucu anahtarı dahil tam
anlatım [AND README'sinde](https://github.com/AND-community/AND/blob/main/README.tr.md#kurulum).

---

## Eklenti Sistemi

AND eklentileri ayrı programlardır. AND her birini kendi binary'sinin yanındaki
`Eklentiler/<isim>/` klasöründe bulur: bir `plugin.json` manifesti ve bir `and-plugin-<isim>`
binary'si. Bir eklentiyi açtığında AND, eklentinin bildirdiği izinleri bir kez sorar; sonra onu aynı
terminalde başlatır ve yerel bir HTTP API'nin adresiyle eklentiye özel bir anahtar verir. Eklenti
kapanınca AND terminali geri alır.

```json
{
  "name":        "ornek",
  "label":       "Örnek Eklenti",
  "version":     "1.0.0",
  "description": "Kısa açıklama",
  "author":      "Yazar Adı",
  "permissions": ["dm"]
}
```

`"label": ""` olan bir eklenti menüde görünmez (konu_ac forumdan `n` ile açılır). Eklentiler AND'ın
arayüz dilinde açılır. Tam şablon ve API başvurusu
[Eklentiler/ornek](https://github.com/AND-community/AND/tree/main/Eklentiler/ornek)'te.

---

## Güvenlik Modeli

```
Kimlik
  128 bit entropi → Ed25519 özel anahtar; cihazında, parolanla
  şifreli durur (Argon2id + AES-256-GCM).
  İmzasız mesaj ağda reddedilir.

Moderasyon
  Kurucu, moderatör sertifikasını Ed25519 ile imzalar.
  Banlar ve silmeler de imzalıdır; her düğüm bağımsız doğrular.
  İptal edilen bir sertifika anında geçersiz olur.

Mesajlar
  Özel mesajlar iki eş arasında doğrudan, şifreli bir libp2p akışıyla
  gider; yerel geçmiş kimlikten türetilen bir anahtarla şifrelenir.

Terminal güvenliği
  Ağdan gelen metin ekrana basılmadan önce kaçış dizilerinden ve kontrol
  karakterlerinden temizlenir; terminalini ele geçiremez.

Eklentiler
  Her eklentinin kendi API anahtarı ve yalnızca senin onayladığın
  izinleri vardır.

Asla paylaşma
  identity.dat   — şifrelenmiş özel anahtarın
  founder.key    — kurucu anahtarı
  bans/          — moderasyon kayıtları
```

Güvenlik açığı bildirmek için [SECURITY.md](https://github.com/AND-community/AND/blob/main/SECURITY.md)'yi
izle — lütfen herkese açık issue açma.

---

## Teknoloji Yığını

| Katman | Kütüphane |
|---|---|
| P2P ağ | `go-libp2p` — DHT (`go-libp2p-kad-dht`), mDNS, GossipSub (`go-libp2p-pubsub`), akışlar |
| TUI | `bubbletea` + `lipgloss` + `bubbles` |
| Yerel depo | `modernc.org/sqlite` (saf Go, CGO gerekmez) |
| Kimlik | Ed25519 (Go standart kütüphanesi) + `tyler-smith/go-bip39` |
| Güncelleme | GitHub Releases API + Ed25519 ile imzalı SHA-256 özetleri |
| Test | Go'nun `testing` paketi, fuzz testleri, iki düğümlü uçtan uca testler |

---

## Katkı

1. Repoyu fork et
2. Feature branch oluştur: `git checkout -b feat/özellik-adı`
3. Değişikliklerini commit et
4. Pull request aç

Katkı rehberi: [CONTRIBUTING.md](https://github.com/AND-community/AND/blob/main/CONTRIBUTING.md).

---

<div align="center">

*AND tamamen Go ile yazılmıştır ve AGPL-3.0 ile lisanslıdır.*  
*Bir sunucuya güvenmek zorunda değilsin.*

</div>
