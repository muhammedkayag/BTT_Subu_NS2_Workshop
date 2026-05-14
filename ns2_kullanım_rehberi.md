# NS2 (Network Simulator 2) Temel Kullanımı

## Basit Ağ Simülasyonu Oluşturma Rehberi

Bu doküman, NS2 kullanarak temel ağ simülasyonlarının nasıl oluşturulduğunu sade ve anlaşılır şekilde anlatmak amacıyla hazırlanmıştır.

---

# 📚 İçindekiler

- NS2 Nedir?
- NS2 Nasıl Çalışır?
- TCL Script Yapısı
- Simülatör Oluşturma
- Node Oluşturma
- Link Kurulumu
- Trafik Oluşturma
- UDP ve CBR Kullanımı
- Simülasyonu Çalıştırma
- NAM ile Görselleştirme
- Tam Örnek Kod

---

# 🌐 1. NS2 Nedir?

**NS2 (Network Simulator 2)**, bilgisayar ağlarını simüle etmek için kullanılan açık kaynaklı bir ağ simülasyon aracıdır.

NS2 sayesinde:

- Ağ topolojileri oluşturulabilir
- Router ve bilgisayar düğümleri eklenebilir
- Trafik simülasyonları yapılabilir
- Paket hareketleri gözlemlenebilir
- Ağ performans analizi yapılabilir
- NAM ile görsel simülasyon izlenebilir

---

# ⚙️ 2. NS2 Nasıl Çalışır?

NS2, **TCL (Tool Command Language)** kullanılarak yönetilir.

Temel çalışma mantığı:

```text
TCL Script Yaz
        ↓
NS2 ile Simülasyonu Çalıştır
        ↓
Trace (.tr) Dosyası Oluşur
        ↓
NAM ile Görselleştir
```

Örnek çalıştırma komutu:

```bash
ns simulation.tcl
```

---

# 🧩 3. Basit TCL Script Yapısı

Bir NS2 scripti genellikle şu bölümlerden oluşur:

1. Simülatör oluşturma
2. Trace dosyası oluşturma
3. Node oluşturma
4. Link bağlama
5. Trafik oluşturma
6. Simülasyonu çalıştırma

---

# 🚀 4. Simülatör Nesnesi Oluşturma

İlk olarak bir simülatör nesnesi oluşturulur.

```tcl
set ns [new Simulator]
```

## Açıklama

| Kod | Açıklama |
|---|---|
| `set` | Değişken tanımlar |
| `ns` | Simülatör değişkeni |
| `new Simulator` | Yeni simülatör oluşturur |

---

# 📄 5. Trace ve NAM Dosyaları Oluşturma

## Trace Dosyası

Simülasyon kayıtlarını tutar.

```tcl
set tracefile [open out.tr w]
$ns trace-all $tracefile
```

---

## NAM Dosyası

NAM görselleştirmesi için kullanılır.

```tcl
set namfile [open out.nam w]
$ns namtrace-all $namfile
```

---

# 🖥️ 6. Node (Düğüm) Oluşturma

Ağdaki cihazlar node olarak tanımlanır.

```tcl
set n0 [$ns node]
set n1 [$ns node]
```

Burada:

- `n0` → Birinci düğüm
- `n1` → İkinci düğüm

Bu düğümler:

- Bilgisayar
- Router
- Sunucu

olarak düşünülebilir.

---

# 🔗 7. Düğümleri Birbirine Bağlama

İki node arasında bağlantı kurulur.

```tcl
$ns duplex-link $n0 $n1 10Mb 10ms DropTail
```

## Parametreler

| Parametre | Açıklama |
|---|---|
| `10Mb` | Bant genişliği |
| `10ms` | Gecikme süresi |
| `DropTail` | Kuyruk algoritması |

---

## Bu Bağlantının Anlamı

- 10 Mbps hız
- 10 ms gecikme
- DropTail kuyruk yönetimi

---

# 📡 8. UDP Trafik Kaynağı Oluşturma

Veri gönderecek ajan oluşturulur.

```tcl
set udp0 [new Agent/UDP]
$ns attach-agent $n0 $udp0
```

Bu işlem UDP ajanını `n0` düğümüne bağlar.

---

# 📥 9. Trafik Alıcısı Oluşturma

Karşı düğüme alıcı eklenir.

```tcl
set null0 [new Agent/Null]
$ns attach-agent $n1 $null0
```

---

# 🔄 10. Gönderici ve Alıcıyı Bağlama

Ajanlar birbirine bağlanır.

```tcl
$ns connect $udp0 $null0
```

Böylece veri aktarımı mümkün hale gelir.

---

# 📈 11. Trafik Üretme (CBR)

CBR (Constant Bit Rate), sabit hızda trafik üretir.

```tcl
set cbr0 [new Application/Traffic/CBR]

$cbr0 set packetSize_ 500
$cbr0 set interval_ 0.01

$cbr0 attach-agent $udp0
```

---

## Parametre Açıklamaları

| Parametre | Açıklama |
|---|---|
| `packetSize_ 500` | 500 byte paket |
| `interval_ 0.01` | Her 0.01 saniyede bir paket |

---

# ⏱️ 12. Trafiği Başlatma ve Durdurma

Simülasyonda zamanlama yapılır.

```tcl
$ns at 1.0 "$cbr0 start"
$ns at 4.0 "$cbr0 stop"
```

## Anlamı

| Zaman | İşlem |
|---|---|
| `1.0` saniye | Trafiği başlat |
| `4.0` saniye | Trafiği durdur |

---

# 🛑 13. Simülasyonu Bitirme

Simülasyon sonunda çalışacak fonksiyon:

```tcl
proc finish {} {

    global ns tracefile namfile

    close $tracefile
    close $namfile

    exec nam out.nam &

    exit 0
}
```

---

## Bu Fonksiyon Ne Yapar?

- Trace dosyasını kapatır
- NAM dosyasını kapatır
- NAM uygulamasını açar
- Simülasyonu sonlandırır

---

# ▶️ 14. Simülasyonu Çalıştırma

```tcl
$ns at 5.0 "finish"

$ns run
```

Bu kod:

- 5. saniyede simülasyonu bitirir
- Simülasyonu çalıştırır

---

# 🧪 15. Tam Örnek NS2 Kodu

```tcl
set ns [new Simulator]

set tracefile [open out.tr w]
$ns trace-all $tracefile

set namfile [open out.nam w]
$ns namtrace-all $namfile

set n0 [$ns node]
set n1 [$ns node]

$ns duplex-link $n0 $n1 10Mb 10ms DropTail

set udp0 [new Agent/UDP]
$ns attach-agent $n0 $udp0

set null0 [new Agent/Null]
$ns attach-agent $n1 $null0

$ns connect $udp0 $null0

set cbr0 [new Application/Traffic/CBR]

$cbr0 set packetSize_ 500
$cbr0 set interval_ 0.01

$cbr0 attach-agent $udp0

proc finish {} {

    global ns tracefile namfile

    close $tracefile
    close $namfile

    exec nam out.nam &

    exit 0
}

$ns at 1.0 "$cbr0 start"
$ns at 4.0 "$cbr0 stop"

$ns at 5.0 "finish"

$ns run
```

---

# 💻 16. Simülasyonu Çalıştırma

## Dosyayı Oluşturma

```bash
nano simulation.tcl
```

---

## Simülasyonu Başlatma

```bash
ns simulation.tcl
```

Simülasyon tamamlandığında NAM otomatik açılır.

---

# 🎞️ 17. NAM ile Görselleştirme

NAM ekranında şunlar gözlemlenebilir:

- Düğümler
- Ağ bağlantıları
- Paket hareketleri
- Trafik yoğunluğu
- Paket kayıpları

Bu sayede ağ davranışı görsel olarak analiz edilir.

---

# 🧮 18. AWK ile Trace Analizi

NS2 simülasyonu tamamlandıktan sonra oluşan `.tr` dosyası analiz edilir.

AWK kullanılarak throughput gibi performans verileri çıkarılabilir.

---

## AWK Scripti Oluşturma

```bash
nano throughput.awk
```

İçerik:

```awk
BEGIN {
    interval = 0.1
}

{
    event = $1
    time  = $2
    pktSize = $6

    # AGT seviyesinde alınan paketler
    if (event == "r" && $4 == "AGT") {

        bytes += pktSize

        if (time >= nextTime) {

            throughput = (bytes * 8) / (interval * 1000)

            print time, throughput

            bytes = 0
            nextTime += interval
        }
    }
}
```

---

# ▶️ 19. AWK Scriptini Çalıştırma

```bash
awk -f throughput.awk out.tr > throughput.dat
```

Bu işlem sonunda:

```text
throughput.dat
```

dosyası oluşur.

---

## Örnek Veri Çıktısı

```text
0.5 120
1.0 240
1.5 310
2.0 450
```

| Sütun | Açıklama |
|---|---|
| 1 | Zaman |
| 2 | Throughput (Kbps) |

---

# 📈 20. Gnuplot ile Grafik Oluşturma

Gnuplot kullanılarak throughput grafiği oluşturulabilir.

---

## Gnuplot Scripti Oluşturma

```bash
nano plot.plt
```

İçerik:

```gnuplot
set terminal png size 800,600
set output "throughput.png"

set title "NS2 Throughput Graph"
set xlabel "Time (s)"
set ylabel "Throughput (kbps)"

set grid

plot "throughput.dat" using 1:2 with lines lw 2 title "Traffic"
```

---

# ▶️ 21. Gnuplot Scriptini Çalıştırma

```bash
gnuplot plot.plt
```

Komut çalıştıktan sonra:

```text
throughput.png
```

dosyası oluşacaktır.

---

# 🖼️ 22. Grafiği Görüntüleme

Linux üzerinde:

```bash
xdg-open throughput.png
```

---

# 📌 23. Tam İş Akışı

```text
NS2 Simülasyonu
        ↓
out.tr oluşur
        ↓
AWK ile throughput.dat oluşturulur
        ↓
Gnuplot ile throughput.png oluşturulur
```

---

# 🎯 Sonuç

NS2, ağ protokollerini ve trafik davranışlarını analiz etmek için güçlü bir simülasyon aracıdır.

Basit TCL komutlarıyla:

- Ağ topolojileri kurulabilir
- Trafik testleri yapılabilir
- Paket hareketleri analiz edilebilir
- Performans ölçümleri gerçekleştirilebilir

AWK ve Gnuplot kullanılarak ise:

- Throughput
- Packet Loss
- Delay
- Jitter

gibi ağ performans metrikleri görsel olarak analiz edilebilir.
