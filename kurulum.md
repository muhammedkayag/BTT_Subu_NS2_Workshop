Ubuntu 16.04 üzerinde NS2 (Network Simulator 2) Kurulum Rehberi
Bu rehber, ns-allinone-2.35 paketinin Ubuntu 16.04 LTS üzerinde kaynaktan derlenerek en sorunsuz ve hızlı şekilde kurulması için hazırlanmıştır.

📋 Gereksinimler
İşletim Sistemi: Ubuntu 16.04 (Xenial Xerus)

İnternet Bağlantısı: Paketleri ve kaynak kodu indirmek için.

🛠️ Adım Adım Kurulum Süreci
1. Sistemi Güncelleme ve Bağımlılıkların Kurulması

Derleme işlemi için gerekli olan temel geliştirme araçlarını ve grafik kütüphanelerini yükleyin:

Bash
sudo apt-get update
sudo apt-get install build-essential autoconf automake libxmu-dev libx11-dev libxt-dev -y
2. Kaynak Kodun İndirilmesi ve Hazırlanması

NS2'nin en stabil sürümü olan 2.35 paketini çekip arşivden çıkarıyoruz:

Bash
wget https://sourceforge.net/projects/nsnam/files/allinone/ns-allinone-2.35/ns-allinone-2.35.tar.gz
tar -xvzf ns-allinone-2.35.tar.gz
cd ns-allinone-2.35
3. Kritik Yama (Linkstate Hatası Çözümü)

Modern derleyicilerde (Ubuntu 16.04 dahil) karşılaşılan linkstate/ls.h hatasını önlemek için şu yamayı uygulayın:

Bash
sed -i 's/.erase/this->erase/g' ns-2.35/linkstate/ls.h
4. Derleme ve Kurulum

Şimdi ana kurulum scriptini çalıştırın. Bu işlem donanımınıza bağlı olarak 5-10 dakika sürebilir:

Bash
./install
⚙️ Ortam Değişkenlerinin (Environment Variables) Yapılandırılması
Kurulum bittikten sonra ns ve nam komutlarının her yerden çalışabilmesi için terminal profilinize yolları eklemeniz gerekir.

.bashrc dosyasını açın:

Bash
nano ~/.bashrc
Dosyanın en altına aşağıdaki satırları yapıştırın (DİKKAT: Eğer klasör ismini değiştirdiyseniz yolları ona göre revize edin):

Bash
   # NS2 Environment Variables
   export PATH=$PATH:$HOME/ns-allinone-2.35/bin:$HOME/ns-allinone-2.35/tcl8.5.10/unix:$HOME/ns-allinone-2.35/tk8.5.10/unix
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/ns-allinone-2.35/otcl-1.14:$HOME/ns-allinone-2.35/lib
   export TCL_LIBRARY=$TCL_LIBRARY:$HOME/ns-allinone-2.35/tcl8.5.10/library
Değişiklikleri kaydedin (CTRL+O, Enter, CTRL+X) ve aktif edin:

Bash
source ~/.bashrc
✅ Kurulumun Doğrulanması
Her şeyin doğru kurulduğunu test etmek için terminale şunu yazın:

Bash
ns
Ekranda % simgesini görüyorsanız kurulum başarıyla tamamlanmıştır. Görselleştirme aracı için ise şu komutu deneyebilirsiniz:

Bash
nam
⚠️ Olası Hatalar ve Çözümler
Görselleştirme Hatası: Eğer nam çalışırken grafik hatası verirse, sanal makine kullanıyorsanız "3D Graphics Acceleration" özelliğini kapatmayı veya X11 yönlendirmesini kontrol etmeyi unutmayın.
