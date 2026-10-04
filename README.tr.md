# The Mentalist — OLED Animasyon

[English](README.md) · [Türkçe](README.tr.md)

*The Mentalist* (Patrick Jane) dizisinin 200 karelik piksel animasyonunun
**128×64 SSD1306 OLED** ekranda, I²C üzerinden Arduino uyumlu bir kartla
oynatılması.

<p align="center">
  <img src="preview.gif" alt="Animasyon önizlemesi" width="480">
</p>

## Önizleme

> Animasyonun ekran kaydını veya GIF'ini depo kök dizinine `preview.gif`
> olarak ekle. Eklenmezse yukarıdaki görsel bozuk ikon olarak görünür.

## Özellikler

- 200 kare, ~7.4 fps (kare başına 135 ms), bir döngü ~27 saniye
- Kare verisi `PROGMEM` içinde saklanıp doğrudan ekrana gönderiliyor — SD kart
  veya çalışma zamanında kare çözümlemesi yok
- `millis()` tabanlı engellenmeyen zamanlama, döngü her zaman yanıt verir
- 115200 baud hızında seri port çıktısı

## Donanım

| Parça | Detay |
|-------|-------|
| Ekran | 0.96" SSD1306 OLED, 128×64, I²C, adres `0x3C` |
| MCU | **En az 256 KB flash olan herhangi bir kart** — ESP32 / ESP8266 / RP2040 önerilir |
| Bağlantı | OLED `VCC` → `3V3`, `GND` → `GND`, `SDA` → `SDA`, `SCL` → `SCL` |

### ⚠️ Klasik AVR kartlar için uygun değil

200 kare × 1024 byte = **~200 KB** kare verisi. ATmega328P (Uno / Nano / Pro
Mini) sadece 32 KB flash alana sahip olduğu için bu kod **Uno veya Nano'da
derlenmez ve çalışmaz**. ESP32 (ya da yeterli flash'ı olan başka bir kart)
kullan, veya AVR'de çalıştırmak için animasyonu ~25 kareye kısalt.

128×64 SSD1306 modüllerinin çoğu sadece 3.3 V ile çalışır — modülün 5 V
desteklediğinden emin değilsen ekranı `5V`'den değil `3V3`'ten besle.

## Kütüphaneler

Arduino IDE kütüphane yöneticisinden kur:

- [Adafruit SSD1306](https://github.com/adafruit/Adafruit_SSD1306)
- [Adafruit GFX Library](https://github.com/adafruit/Adafruit-GFX-Library)
- [Adafruit BusIO](https://github.com/adafruit/Adafruit_BusIO) (bağımlılık)

## Kullanım

1. `theMentalistOledEdit.ino` dosyasını Arduino IDE'de aç.
2. Kartını ve doğru portu seç.
3. Yükle, ardından ekranın başarıyla tanındığını doğrulamak için Seri Monitör'ü
   **115200 baud** hızında aç.
4. Animasyon otomatik olarak döngüye girer.

Ekran boş kalıyorsa `0x3C` yerine `0x3D` adresini dene ve SDA/SCL bağlantısını
kontrol et — ESP32'de varsayılan olarak `GPIO 21` (SDA) ve `GPIO 22` (SCL)
kullanılır.

## Yapılandırma

```cpp
#define SCREEN_WIDTH  128
#define SCREEN_HEIGHT 64
#define OLED_RESET    -1
#define SCREEN_ADDR   0x3C
```

Kare sayısı ve zamanlama:

```cpp
const uint8_t numFrames = 200;

if (millis() - lastMs >= 135) {   // düşürürsen animasyon hızlanır
  ...
}
```

Animasyonun sadece bir bölümünü oynatmak için kullanılmayan kareleri sil ve
`numFrames` değerini düşür.

## Katkıda bulunanlar / Kredi

Kare görselleri **Ashish** (`ashish_y_794`) tarafından
[oledanimationmaker.com](https://www.oledanimationmaker.com/) üzerinden üretildi.
Bu sketch'teki kare dizileri o jeneratörden geliyor ve orijinal yazarına
kredileri durdurulmuştur.

## Lisans

Bu depodaki kodun lisansı MIT'tir. Kare verisi üçüncü taraf bir araçla üretilmiş
olup yukarıda belirtildiği gibi orijinal yazarına aittir — ticari kullanım
öncesi jeneratörün sitesindeki koşulları kontrol et.