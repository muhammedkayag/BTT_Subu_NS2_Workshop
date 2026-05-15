# NS2 Queue Algoritmaları Karşılaştırma Rehberi
## DropTail ve RED Queue Algoritmalarının Simülasyonu

Bu çalışmada:

- NS2 kullanılarak ağ simülasyonu oluşturulacaktır.
- Aynı topoloji üzerinde:
  - **DropTail**
  - **RED (Random Early Detection)**

  kuyruk algoritmaları test edilecektir.
- Trafik analizi yapılacaktır.
- Sonuçlar GNUPlot ile grafik haline getirilecektir.

---

# 1. İlk TCL Dosyasını Oluşturma (DropTail)

Önce terminalden aşağıdaki komut çalıştırılır:

```bash
nano odev2_isim.tcl
```

Ardından aşağıdaki kodlar dosyanın içine yapıştırılır.

---

## DropTail Simülasyon Kodu

```tcl
# Simulator
set ns [new Simulator]

# Trace dosyaları
set tracefile [open "isimDropTailA.tr" w]
$ns trace-all $tracefile

# NAM animasyon
set namfile [open "isim.nam" w]
$ns namtrace-all $namfile

# 6 Node oluştur
for {set i 0} {$i < 6} {incr i} {
    set n($i) [$ns node]
}

# Mesh topoloji (herkes herkese bağlı)
for {set i 0} {$i < 6} {incr i} {
    for {set j [expr $i+1]} {$j < 6} {incr j} {
        $ns duplex-link $n($i) $n($j) 1Mb 10ms DropTail
        $ns duplex-link-op $n($i) $n($j) color blue
    }
}

# ---------- TRAFİKLER ----------

# Trafik 1 (TCP - Kırmızı) n0 → n3
set tcp1 [new Agent/TCP]
$tcp1 set class_ 1
$ns attach-agent $n(0) $tcp1

set sink1 [new Agent/TCPSink]
$ns attach-agent $n(3) $sink1

$ns connect $tcp1 $sink1

set ftp1 [new Application/FTP]
$ftp1 attach-agent $tcp1


# Trafik 2 (UDP/CBR - Yeşil) n1 → n4
set udp1 [new Agent/UDP]
$ns attach-agent $n(1) $udp1

set null1 [new Agent/Null]
$ns attach-agent $n(4) $null1

$ns connect $udp1 $null1

set cbr1 [new Application/Traffic/CBR]
$cbr1 attach-agent $udp1
$cbr1 set rate_ 500Kb


# Trafik 3 (TCP - Sarı) n2 → n5
set tcp2 [new Agent/TCP]
$tcp2 set class_ 2
$ns attach-agent $n(2) $tcp2

set sink2 [new Agent/TCPSink]
$ns attach-agent $n(5) $sink2

$ns connect $tcp2 $sink2

set ftp2 [new Application/FTP]
$ftp2 attach-agent $tcp2


# Renkler
$ns color 1 Red
$ns color 2 Yellow

# Zamanlama (max 30 sn)
$ns at 1.0 "$ftp1 start"
$ns at 2.0 "$cbr1 start"
$ns at 3.0 "$ftp2 start"

$ns at 25.0 "$ftp1 stop"
$ns at 26.0 "$cbr1 stop"
$ns at 27.0 "$ftp2 stop"

$ns at 30.0 "finish"

# Finish
proc finish {} {
    global ns tracefile namfile
    $ns flush-trace
    close $tracefile
    close $namfile
    exec nam isim.nam &
    exit 0
}

$ns run
```

---

# 2. Bu Kod Ne Yapıyor?

Bu simülasyonda:

- 6 adet node oluşturuluyor.
- Tüm node’lar birbirine bağlanıyor.
- Ağ topolojisi tam bağlı (**mesh topology**) oluyor.
- Kuyruk algoritması olarak:

```tcl
DropTail
```

kullanılıyor.

---

# 3. Trafik Türleri

Simülasyonda 3 farklı trafik bulunmaktadır:

| Trafik | Tür | Kaynak | Hedef |
|---|---|---|---|
| Trafik 1 | TCP/FTP | n0 | n3 |
| Trafik 2 | UDP/CBR | n1 | n4 |
| Trafik 3 | TCP/FTP | n2 | n5 |

---

# 4. DropTail Algoritması Nedir?

DropTail en basit kuyruk yönetim algoritmasıdır.

Mantığı:

- Kuyruk dolana kadar paketleri kabul eder.
- Kuyruk tamamen dolunca yeni gelen paketleri düşürür.

## Avantajları

- Basit
- Hızlı

## Dezavantajları

- Ağ tıkanıklığında ani paket kaybı oluşturur.
- TCP senkronizasyon problemleri oluşabilir.

---

# 5. RED Algoritması İçin Yeni Dosya Oluşturma

Şimdi RED algoritması için ikinci TCL dosyası oluşturulur.

Terminalde:

```bash
nano odev2_isim_red.tcl
```

komutu çalıştırılır.

---

## RED Simülasyon Kodu

```tcl
# Simulator
set ns [new Simulator]

# Trace dosyaları
set tracefile [open "isimREDB.tr" w]
$ns trace-all $tracefile

# NAM animasyon
set namfile [open "isim.nam" w]
$ns namtrace-all $namfile

# 6 Node oluştur
for {set i 0} {$i < 6} {incr i} {
    set n($i) [$ns node]
}

# Mesh topoloji (herkes herkese bağlı)
for {set i 0} {$i < 6} {incr i} {
    for {set j [expr $i+1]} {$j < 6} {incr j} {
        $ns duplex-link $n($i) $n($j) 1Mb 10ms RED
        $ns duplex-link-op $n($i) $n($j) color blue
    }
}

# ---------- TRAFİKLER ----------

# Trafik 1 (TCP - Kırmızı) n0 → n3
set tcp1 [new Agent/TCP]
$tcp1 set class_ 1
$ns attach-agent $n(0) $tcp1

set sink1 [new Agent/TCPSink]
$ns attach-agent $n(3) $sink1

$ns connect $tcp1 $sink1

set ftp1 [new Application/FTP]
$ftp1 attach-agent $tcp1


# Trafik 2 (UDP/CBR - Yeşil) n1 → n4
set udp1 [new Agent/UDP]
$ns attach-agent $n(1) $udp1

set null1 [new Agent/Null]
$ns attach-agent $n(4) $null1

$ns connect $udp1 $null1

set cbr1 [new Application/Traffic/CBR]
$cbr1 attach-agent $udp1
$cbr1 set rate_ 500Kb


# Trafik 3 (TCP - Sarı) n2 → n5
set tcp2 [new Agent/TCP]
$tcp2 set class_ 2
$ns attach-agent $n(2) $tcp2

set sink2 [new Agent/TCPSink]
$ns attach-agent $n(5) $sink2

$ns connect $tcp2 $sink2

set ftp2 [new Application/FTP]
$ftp2 attach-agent $tcp2


# Renkler
$ns color 1 Red
$ns color 2 Yellow

# Zamanlama (max 30 sn)
$ns at 1.0 "$ftp1 start"
$ns at 2.0 "$cbr1 start"
$ns at 3.0 "$ftp2 start"

$ns at 25.0 "$ftp1 stop"
$ns at 26.0 "$cbr1 stop"
$ns at 27.0 "$ftp2 stop"

$ns at 30.0 "finish"

# Finish
proc finish {} {
    global ns tracefile namfile
    $ns flush-trace
    close $tracefile
    close $namfile
    exec nam isim.nam &
    exit 0
}

$ns run
```

---

# 6. RED Algoritması Nedir?

RED (**Random Early Detection**) daha gelişmiş bir kuyruk algoritmasıdır.

## Çalışma Mantığı

- Kuyruk tamamen dolmadan önce paket düşürmeye başlar.
- Böylece ağ tıkanıklığını erken fark eder.
- TCP bağlantılarının hızını kontrollü şekilde azaltmasını sağlar.

## Avantajları

- Daha stabil ağ performansı
- Daha az ani paket kaybı
- Daha düşük gecikme

---

# 7. RED Simülasyonunu Çalıştırma

Aşağıdaki komut çalıştırılır:

```bash
ns odev2_isim_red.tcl
```

Bu işlem sonunda:

- `.tr` trace dosyası oluşur.
- `.nam` animasyon dosyası oluşur.

---

# 8. Trace Dosyalarından Veri Çıkarma

Şimdi paket sayılarını analiz etmek için aşağıdaki komut çalıştırılır.

```bash
awk '{ if ($1=="r") print int($2) }' isimDropTailA.tr | sort -n | uniq -c | awk '{print $2, $1}' > drop2.txt && awk '{ if ($1=="r") print int($2) }' isimREDB.tr | sort -n | uniq -c | awk '{print $2, $1}' > red2.txt
```

---

# 9. Bu AWK Komutu Ne Yapıyor?

Bu komut:

- Trace dosyasındaki alınan (`r`) paketleri filtreler.
- Zaman bilgilerini alır.
- Her saniyedeki paket sayılarını hesaplar.
- Sonuçları:

```text
drop2.txt
red2.txt
```

dosyalarına kaydeder.

---

# 10. GNUPlot Dosyası Oluşturma

Şimdi grafik çizmek için GNUPlot script dosyası oluşturulur.

```bash
nano grafik.plt
```

---

## GNUPlot Kodları

```gnuplot
set terminal pngcairo size 900,600 enhanced font "Arial,10"
set output "isim_graph.png"

set title "Queue Algorithm Comparison (isim)"
set xlabel "Time (seconds)"
set ylabel "Packets per second"

set grid
set key outside

set style line 1 lt 1 lw 3 lc rgb "#FF0000"
set style line 2 lt 1 lw 3 lc rgb "#0000FF"

plot "drop2.txt" using 1:2 with linespoints ls 1 title "DropTail", \
     "red2.txt" using 1:2 with linespoints ls 2 title "RED"

set output
```

---

# 11. Grafik Oluşturma

Grafiği oluşturmak için:

```bash
gnuplot grafik.plt
```

komutu çalıştırılır.

---

# 12. Oluşan Grafik

Bu işlem sonunda:

```text
isim_graph.png
```

isimli grafik oluşacaktır.

Grafikte:

- X ekseni → Zaman
- Y ekseni → Paket sayısı

gösterilir.

---

# 13. Beklenen Sonuç

Genellikle:

## DropTail

- Daha ani düşüşler gösterir.
- Trafik dalgalanmaları fazladır.

## RED

- Daha stabil çalışır.
- Trafik daha dengelidir.

Bu nedenle RED çoğu durumda daha gelişmiş bir kuyruk yönetim algoritması olarak kabul edilir.
