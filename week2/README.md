# Hafta 2 – nRF24L01 ile AES-128 Şifreli Kablosuz Haberleşme

## Grup

| Üye            |
|----------------|
| Tolgahan Keleş |
| Feyyaz Kavun   |

**Grup görevi:** **2412 MHz** kanalında (nRF24L01 kanal 12) **alıcı (receiver)** kısmının gerçeklenmesi.

**Bireysel görev:** Kriptografi yöntemlerinin araştırılıp kısaca özetlenmesi (Tolgahan Keleş) → [KRIPTO_YONTEMLERI.md](KRIPTO_YONTEMLERI.md)

## Genel Bakış

Bu örnekte iki ESP32 kartı, nRF24L01 modülleri üzerinden 2.4 GHz bandında haberleşir.
Verici (`sender`) her saniye `hello1`, `hello2`, ... şeklinde bir mesaj üretir, bu mesajı
AES-128 ile şifreler ve gönderir. Alıcı (`receiver`) gelen paketi çözer ve hem şifreli
hem de çözülmüş hâlini seri porta yazar.

Grubumuzun teslim ettiği dosya `receiver/receiver.ino`'dur. `sender/sender.ino` yalnızca
alıcıyı uçtan uca test edebilmek için eklenmiştir.

```
lecture/
├── README.md
├── KRIPTO_YONTEMLERI.md    # Kriptografi yöntemleri araştırma özeti
├── sender/sender.ino       # Verici (test için): mesaj üret → şifrele → gönder
└── receiver/receiver.ino   # Alıcı (görevimiz): paketi al → şifreyi çöz → yazdır
```

## Gerekli Malzemeler

- 2 × ESP32 geliştirme kartı
- 2 × nRF24L01 (veya nRF24L01+PA+LNA) modülü
- Jumper kablolar

## Bağlantı Şeması

Her iki kartta da bağlantılar aynıdır:

| nRF24L01 | ESP32   |
|----------|---------|
| VCC      | 3.3V    |
| GND      | GND     |
| CE       | GPIO 22 |
| CSN      | GPIO 5  |
| SCK      | GPIO 18 |
| MOSI     | GPIO 23 |
| MISO     | GPIO 19 |
| IRQ      | Bağlanmaz |

## Yazılım Kurulumu

1. Arduino IDE'ye **ESP32 kart paketini** (Espressif) kurun. AES için kullanılan
   `mbedtls` kütüphanesi bu paketle birlikte gelir, ayrıca kurulum gerekmez.
2. Library Manager'dan **RF24** (TMRh20) kütüphanesini kurun.
3. `receiver/receiver.ino` dosyasını bir karta, test için `sender/sender.ino` dosyasını diğer karta yükleyin.
4. Her iki kart için Serial Monitor'ü **115200 baud** ile açın.

## Haberleşme Ayarları

İki tarafta da aşağıdaki ayarlar **birebir aynı** olmalıdır, aksi hâlde paketler alınamaz:

| Parametre      | Değer                          |
|----------------|--------------------------------|
| Kanal          | 12 → 2400 + 12 = **2412 MHz**  |
| Adres          | `"1Node"`                      |
| Veri hızı      | 1 Mbps                         |
| CRC            | 16 bit                         |
| Payload boyutu | 32 bayt (sabit)                |
| Auto-ACK       | Kapalı                         |
| AES anahtarı   | `00 01 02 ... 0F` (128 bit)    |

## Nasıl Çalışır?

**Alıcı (görevimiz)**
1. `radio.available()` ile RX FIFO'da paket olup olmadığı kontrol edilir.
2. Paket okunur, iki adet 16 baytlık blok ayrı ayrı AES-128 ECB ile çözülür.
3. Şifreli veri (hex) ve çözülmüş mesaj seri porta yazılır.

**Verici (test için)**
1. 32 baytlık tampon sıfırlanır, içine `helloN` yazılır.
2. Tampon iki adet 16 baytlık bloğa bölünür ve her blok AES-128 ECB ile şifrelenir.
3. Şifreli 32 bayt `radio.write()` ile gönderilir.
4. Düz metin ve şifreli veri (hex) seri porta yazılır. Bu işlem saniyede bir tekrarlanır.

### Örnek Çıktı

Aşağıdaki hex değerleri temsilidir; gerçek çıktı farklı olacaktır.

Alıcı:
```
Listening on 2412 MHz (AES-128 ECB)...
Encrypted (hex): 3A 9F ...  | Decrypted: hello1
```

Verici:
```
Transmitting on 2412 MHz (AES-128 ECB)...
Sent: hello1 | Encrypted (hex): 3A 9F ...
```
