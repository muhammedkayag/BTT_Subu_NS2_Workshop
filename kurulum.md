# Ubuntu 16.04 Üzerinde NS2 (Network Simulator 2) Kurulum Rehberi

Bu rehber, `ns-allinone-2.35` paketinin Ubuntu 16.04 LTS üzerinde kaynak koddan derlenerek stabil ve sorunsuz şekilde kurulması için hazırlanmıştır.

---

# 📋 Gereksinimler

- İşletim Sistemi: Ubuntu 16.04 LTS (Xenial Xerus)
- İnternet bağlantısı
- Terminal erişimi
- `sudo` yetkisi

---

# 🛠️ Kurulum Adımları

## 1. Sistemi Güncelleyin ve Gerekli Paketleri Kurun

Derleme işlemi için gerekli bağımlılıkları yükleyin:

```bash
sudo apt-get update

sudo apt-get install build-essential autoconf automake \
libxmu-dev libx11-dev libxt-dev -y
```

---

## 2. NS2 Kaynak Kodunu İndirin

`ns-allinone-2.35` paketini indirip arşivden çıkarın:

```bash
wget https://sourceforge.net/projects/nsnam/files/allinone/ns-allinone-2.35/ns-allinone-2.35.tar.gz && tar -xvzf ns-allinone-2.35.tar.gz && cd ns-allinone-2.35
```

---

## 3. Linkstate Hatası İçin Kritik Yamayı Uygulayın

Ubuntu 16.04 ve modern GCC sürümlerinde oluşabilen `linkstate/ls.h` hatasını düzeltmek için aşağıdaki komutu çalıştırın:

```bash
sed -i 's/.erase/this->erase/g' ns-2.35/linkstate/ls.h && sed -i 's/voidthis/void/g' ns-2.35/linkstate/ls.h && sed -i 's/void->/void /g' ns-2.35/linkstate/ls.h
```

---

## 4. NS2 Kurulumunu Başlatın

Kurulum scriptini çalıştırın:

```bash
./install
```

> Kurulum işlemi sistem performansına bağlı olarak yaklaşık 5-10 dakika sürebilir.

---

# ⚙️ Ortam Değişkenlerini Yapılandırma

Kurulum tamamlandıktan sonra `ns` ve `nam` komutlarının terminalin her yerinden çalışabilmesi için environment variable tanımlaması yapılmalıdır. Burdan sonraki komutlar kullanılırken root kullanıcısı 
ile işlem yapmamanız gerekmektedir.

## `.bashrc` Dosyasını Açın

```bash
nano ~/.bashrc
```

## Dosyanın En Altına Şu Satırları Ekleyin

```bash
# NS2 Environment Variables

export PATH=$PATH:/home/$USER/ns-allinone-2.35/bin:/home/$USER/ns-allinone-2.35/tcl8.5.10/unix:/home/SUSER/ns-allinone-2.35/tk8.5.10/unix
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/$USER/ns-allinone-2.35/otcl-1.14:/home/$USER/ns-allinone-2.35/lib
export TCL_LIBRARY=$TCL_LIBRARY:/home/$USER/ns-allinone-2.35/tcl8.5.10/library
```

> Eğer klasör adını değiştirdiyseniz yolları kendi dizininize göre düzenleyin.

## Değişiklikleri Kaydedin ve Aktif Edin

```bash
source ~/.bashrc
```

---

# ✅ Kurulum Doğrulama

## NS2 Testi

```bash
ns
```

Eğer terminalde aşağıdaki gibi `%` işareti görünüyorsa kurulum başarılıdır:

```bash
%
```

Çıkmak için:

```bash
exit
```

---

## NAM Görselleştirme Aracı Testi

```bash
nam
```

---

# ⚠️ Olası Hatalar ve Çözümleri

## Görselleştirme (NAM) Hatası

Eğer `nam` çalışırken pencere açılmıyor veya grafik hatası alıyorsanız:

- Sanal makine kullanıyorsanız:
  - `3D Graphics Acceleration` özelliğini kapatın
- X11 forwarding kullanıyorsanız:
  - X11 yapılandırmasını kontrol edin
- VirtualBox kullanıyorsanız:
  - Guest Additions kurulu olduğundan emin olun

---

# 📌 Faydalı Komutlar

## NS2 Versiyon Kontrolü

```bash
ns -v
```

## Kurulum Dizini Kontrolü

```bash
ls ~/ns-allinone-2.35
```

## Environment Variable Kontrolü

```bash
echo $PATH
```

---

# 🎯 Sonuç

Bu adımları tamamladıktan sonra Ubuntu 16.04 üzerinde NS2 başarıyla kurulmuş olacaktır. Artık TCL tabanlı ağ simülasyonlarını çalıştırabilir ve `NAM` ile görselleştirme yapabilirsiniz.
