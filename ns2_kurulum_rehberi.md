# NS2 (Network Simulator 2) Temel Kullanımı  
## Basit Ağ Simülasyonu Oluşturma Rehberi

---

# 1. NS2 Nedir?

**NS2 (Network Simulator 2)**, bilgisayar ağlarını simüle etmek için kullanılan açık kaynaklı bir simülasyon aracıdır.

NS2 ile şunlar yapılabilir:

- Ağ topolojisi oluşturma
- Düğümler (node) ekleme
- Bağlantılar kurma
- Trafik üretme
- Paket hareketlerini gözlemleme
- Performans analizi yapma
- NAM ile görsel simülasyon izleme

---

# 2. NS2 Nasıl Çalışır?

NS2, **TCL (Tool Command Language)** kullanılarak kontrol edilir.

Temel çalışma mantığı:

```text
TCL Script Yaz → NS2 ile Çalıştır → Trace Dosyası Oluşur → NAM ile Görselleştir
```

Örnek:

```bash
ns simulation.tcl
```

---

# 3. Basit TCL Script Yapısı

Bir NS2 dosyası genellikle şu bölümlerden oluşur:

1. Simülatör oluşturma
2. Düğümleri oluşturma
3. Linkleri bağlama
4. Trafik tanımlama
5. Simülasyonu başlatma

---

# 4. Simülatör Nesnesi Oluşturma

İlk olarak NS2 simülatörü başlatılır.

```tcl
set ns [new Simulator]
```

Buradaki:

- `set` → değişken tanımlar
- `ns` → simülatör değişkeni
- `new Simulator` → yeni simülatör oluşturur

---

# 5. Trace ve NAM Dosyaları Oluşturma

Simülasyon sonuçlarını kaydetmek için:

```tcl
set tracefile [open out.tr w]
$ns trace-all $tracefile
```

NAM görselleştirmesi için:

```tcl
set namfile [open out.nam w]
$ns namtrace-all $namfile
```

---

# 6. Düğüm (Node) Oluşturma

Ağdaki cihazlar node olarak tanımlanır.

Örnek:

```tcl
set n0 [$ns node]
set n1 [$ns node]
```

Burada iki farklı düğüm oluşturduk:

- `n0`
- `n1`

Bu düğümler bilgisayar, router veya sunucu olabilir.

---

# 7. Düğümleri Birbirine Bağlama

İki düğüm arasında bağlantı kurulur.

```tcl
$ns duplex-link $n0 $n1 10Mb 10ms DropTail
```

Anlamı:

- `10Mb` → bant genişliği
- `10ms` → gecikme
- `DropTail` → kuyruk algoritması

Yani:

**10 Mbps hızında, 10 ms gecikmeli bağlantı**

---

# 8. Trafik Kaynağı Oluşturma (UDP)

Veri göndermek için önce bir ajan oluşturulur.

```tcl
set udp0 [new Agent/UDP]
$ns attach-agent $n0 $udp0
```

Bu ajan `n0` düğümüne bağlanır.

---

# 9. Trafik Alıcısı Oluşturma

Karşı tarafa alıcı eklenir.

```tcl
set null0 [new Agent/Null]
$ns attach-agent $n1 $null0
```

---

# 10. Gönderici ve Alıcıyı Bağlama

İki ajan birbirine bağlanır.

```tcl
$ns connect $udp0 $null0
```

Artık veri gönderilebilir.

---

# 11. Trafik Üretme (CBR)

Sabit hızda trafik üretmek için CBR kullanılır.

```tcl
set cbr0 [new Application/Traffic/CBR]
$cbr0 set packetSize_ 500
$cbr0 set interval_ 0.01
$cbr0 attach-agent $udp0
```

Anlamları:

- `packetSize_ 500` → 500 byte paket
- `interval_ 0.01` → her 0.01 saniyede bir paket

---

# 12. Trafiği Başlatma ve Durdurma

Simülasyon zamanlaması yapılır.

```tcl
$ns at 1.0 "$cbr0 start"
$ns at 4.0 "$cbr0 stop"
```

Yani:

- 1. saniyede başla
- 4. saniyede dur

---

# 13. Simülasyonu Bitirme

Bitirme fonksiyonu:

```tcl
proc finish {} {
    global ns tracefile namfile
    close $tracefile
    close $namfile
    exec nam out.nam &
    exit 0
}
```

Simülasyon sonunda:

- Dosyalar kapanır
- NAM açılır

---

# 14. Simülasyonu Çalıştırma

```tcl
$ns at 5.0 "finish"
$ns run
```

---

# 15. Tam Örnek Kod

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

# 16. Simülasyonu Çalıştırma

Dosyayı kaydet:

```bash
nano simulation.tcl
```

Çalıştır:

```bash
ns simulation.tcl
```

NAM otomatik açılacaktır.

---

# 17. NAM ile Görselleştirme

NAM penceresinde şunlar görülebilir:

- Düğümler
- Linkler
- Paket hareketleri
- Trafik yoğunluğu
- Paket kayıpları

Bu sayede ağ davranışı görsel olarak incelenebilir.

---

# 18. Özet

NS2 ile temel adımlar:

1. Simülatör oluştur
2. Node oluştur
3. Link kur
4. Trafik tanımla
5. Zamanlama yap
6. Simülasyonu çalıştır
7. NAM ile izle

---

# Sonuç

NS2, ağ protokollerini ve trafik davranışlarını test etmek için güçlü bir araçtır.  
Basit TCL komutlarıyla farklı ağ senaryoları oluşturulabilir ve analiz edilebilir.
