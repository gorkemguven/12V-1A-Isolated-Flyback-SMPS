# 12V 1A (12W) Isolated Flyback SMPS Power Supply

Bu proje, 230V AC şebeke gerilimini izole ve kararlı bir 12V DC çıkış gerilimine dönüştüren kompakt bir **Flyback Anahtarlamalı Güç Kaynağı (SMPS)** donanım tasarımıdır. Trafo hesaplamaları, sarımı, manyetik tasarımı, devre şematiği ve PCB yerleşimi baştan sona özel olarak tasarlanmıştır.

---

## 📌 Genel Özellikler

* **Giriş Gerilimi:** 85V – 265V AC (Evrensel Şebeke Girişi)
* **Çıkış Gerilimi:** 12.0V DC
* **Çıkış Akımı / Gücü:** 1A / 12W
* **Topoloji:** Current-Mode Isolated Flyback Converter
* **Kontrol Entegresi:** UC3844 Current-Mode PWM Controller
* **Geri Besleme Elemanı:** TL431 Hassas Programlanabilir Referans + PC817 Optokuplör
* **Frekans Kompanzasyonu:** Tip-2 (Type-2) Seri RC Ağı
* **Tasarım Aracı:** KiCad EDA

---

## 🛠️ Devre Mimarisi ve Çalışma Mantığı

### 1. Giriş ve EMI Filtreleme Katı
* **Giriş Koruması:** Sigorta (F1) ve Varistör (RV2) ile aşırı akım ve yüksek gerilim dalgalanmalarına (surge) karşı tam koruma.
* **Filtreleme:** Diferansiyel ve ortak mod gürültülerini bastırmak için X2 sınıfı film kondansatör ve ortak mod bobini (FL2).
* **Doğrultma & DC Bara:** Köprü diyot ve yüksek gerilimli elektrolitik filtre kondansatörü ile ~320V DC primer gerilim barası.

### 2. Güç, Manyetik Tasarım ve Anahtarlama Katı
* **Özel Trafo Sarımı:** Primer, sekonder ve yardımcı (auxiliary) sargılar kaçak endüktansı (leakage inductance) en aza indirecek ve yüksek gerilim izolasyon standartlarını karşılayacak şekilde özel olarak hesaplanıp sarılmıştır.
* **RCD Snubber Devresi:** MOSFET anahtarlama anında trafonun kaçak endüktansından kaynaklanan yüksek gerilim iğnelerini sönümleyerek anahtarlama elemanını korur.
* **Akım Algılama (ISENSE):** MOSFET Source bacağına bağlı şönt direnç ile primer akımı periyot bazında anlık olarak izlenir ve sınırlandırılır.

### 3. İzole Geri Besleme ve Frekans Kompanzasyonu
Çıkış gerilimini tam 12.0V'ta kilitlemek için kapalı çevrim geri besleme mimarisi kurulmuştur:
* **Hassas Gerilim Bölücü:** R6 (10kΩ) ve R26 (2.63kΩ) dirençleri ile 12V çıkış tam 2.50V referans seviyesine bölünür.
* **Galvanik İzolasyon:** PC817 optokuplör, sekonder gerilim hatasını primer taraftaki UC3844'ün `COMP` (Pin 1) ucuna elektriksel bağ olmadan ışık yoluyla aktarır.
* **Tip-2 RC Kompanzasyonu:** TL431'in Reference ve Cathode pinleri arasına eklenen seri RC devresi (10kΩ + 100nF), döngüye faz avansı kazandırarak dinamik yük değişimlerinde rezonansı, çıkış voltaj dalgalanmasını (ripple) ve transformatör ötme sesini engeller.

---

## 📋 Geri Besleme Durum Tablosu

| Durum | TL431 (Ref / Katot) | Optokuplör LED | UC3844 COMP Gerilimi | PWM Duty Cycle | Çıkış Tepkisi |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **$V_{out} > 12.0\text{V}$** | Eşik aşılır (>2.5V), iletime geçer | Parlak yanar, primer transistör açılır | Primer GND'ye çekilir (düşer) | Daralır (Frenleme) | Gerilim 12.0V'a geriler |
| **$V_{out} < 12.0\text{V}$** | Eşik altına iner (<2.5V), kesime girer | Söner, primer transistör yalıtımda | Dahili kaynakla yükselir (~4V) | Genişler (Gaz verme) | Gerilim 12.0V'a toparlar |

---

## 📐 PCB Tasarım ve İzolasyon Standartları

* **Creepage & Clearance:** Yüksek gerilim taşıyan primer tarafı ile sekonder tarafı arasında uluslararası izolasyon standartlarına uygun yalıtım mesafesi bırakılmıştır.
* **EMI Optimizasyonu:** Yüksek $dv/dt$ üreten MOSFET Drain hattı minimum döngü alanıyla yönlendirilerek gürültü yayılımı en aza indirilmiştir.
* **Termal ve Empedans Yönetimi:** D+ ve Primer GND düzlemleri geniş bakır alanlar olarak tasarlanmış, akım taşıma kapasitesi ve soğutma performansı artırılmıştır.

---

## 📁 Proje Dosyaları

* `flyback trafo devresi_SCH.pdf`: Ayrıntılı şematik çizim çıktısı.
* `flyback_trafo_devresi.zip`: PCB üretim (Gerber) dosyaları.
* `bom.csv`: Projede kullanılan komponentlerin malzeme listesi.
* `positions.csv` & `designators.csv`: Otomatik dizgi (Pick & Place) konumlandırma dosyaları.

---

## ⚠️ Güvenlik Uyarısı
Bu kart şebeke gerilimi (230V AC) ve yüksek DC bara gerilimi (~320V-400V DC) ile çalışır. Test ve ölçüm süreçlerinde **izolasyon trafosu** kullanılmalı, elektrik güvenliği kurallarına eksiksiz uyulmalıdır.
