# TFT35V1.0-Klipper-screen "This Guide Taken From Mr Zafer Github"
"Gelecek olan yeni paket güncellemelerinin olası hatalara sebep olmaması için ""Tüm paketleri Kiauh hariç kendi github hesabımda depoluyorum".

Orjinal dökümantasyon kaynağı= [Mr.Zafer](https://github.com/zavarci/TFT35V1.0-Klipper-screen) github


![main](https://github.com/technical50/TFT35V1.0-Klipper-screen/blob/main/IMG_1216.jpg)

Öncelikle ekranda tek bir donanım değişikliği yapmanız gerekiyor.
MKS TFT35 V1.0 ekranı, dokunmatik ekrandan gelen kesme çıkışı lehimlenmemiştir. Bunu tek bir jumper ile düzeltmek kolaydır.  Bu iyileştirme olmadan sensör anketi olmayacak, dolayısıyla sistemde dokunma çalışmıyor. Marlinde sensörünün nasıl etkinleştirildiğine dair en ufak bir fikrim yok.
Buradan görebildiğim kadarıyla [schematics](https://github.com/technical50/TFT35V1.0-Klipper-screen/blob/main/Improvement%20on%20the%20screen%20board/sch_mks_tft35_int_ts.GIF), bu değişikliğin marlin'e hiçbir zararı yoktur.
Karttaki jumper bağlantısı şu şekilde.

![main](https://github.com/technical50/TFT35V1.0-Klipper-screen/blob/main/Improvement%20on%20the%20screen%20board/jumper%20cooper.jpg)

Çok zor değil resim biraz elinizden geliyorsa bu kabloyu atarsınız.



Yazma/düzenleme aşamasında.



3 Seçeneğiniz mevcut
1) Hiçbir şey yüklü değil, ardından "Sıfırdan yükleme" öğesine gidin.
2) Çalışan Bir klipper varsa, klipperscreen yoksa talimatları izleyip Klipperscreen kurun.
3) Zaten klipper screen varsa (örneğin, HDMI), ve siz 3.5 inç ekranı devreye almak istiyorsanız. - TFT 35 kurulumuna geçebilirsiniz.

Raspberry imaj yükleme programı ile [Raspberry Pi Imagerupload](https://www.raspberrypi.com/software/) Mikro sd kartınıza Mainsail kurulumunu yapmalısınız.
Sisteme SSH ile bağlanarak [PUTTY](https://www.putty.org/) GIT kurulum yapılacak:
```shell
sudo apt install git
```
Sonraki Kurulum [KIAUH](https://github.com/th33xitus/kiauh), Klipper'ı ve tüm bileşenlerini bunun üzerinden kuracağız. Komutları sırayla giriyoruz:
```shell
cd ~
git clone https://github.com/th33xitus/kiauh.git
./kiauh/kiauh.sh
```
Herşey planlandığı gibi gittiyse resimdeki ekranı görüyor olmanız gerekli.
![main](https://github.com/technical50/TFT35V1.0-Klipper-screen/blob/main/pictures/kiauh.PNG)  
Takiben sıralamaya göre yüklemeri tamamlayın Hata ile karşılaşırsanız devam etmeyin, hatanın ne olduğunu neyin sebep olup anladıktan sonra kurulumu tamamlamanız gerekli.
Klipper (1 pc) - gerekli. Eğer birden fazla yazıcınız varsa bunu da ayarlamanız gerekli, eğer ne yaptığınızı bilmiyorsanız eğitim vermeyeceğiz devam edin!
Moonraker (1 pc) - gerekli.
Fluidd yada Mansail birini seçin ben Mainsail seçtim . (not eğer raspbery kurulumunda direk bunlar geliyor eğer varsa tekrar kurup yapılandırmaları tamamlayın yoksa gerek yok)
Klipperscreen 

Tüp adımlar tamamsa artık işimize başlayalım çalışan bir Mainsail , Fluidd vs elinizde mevcut demektir.
Klipper screen kurulum yaptıysanız buraya kadar herşey tamam.


# TFT35 setup

# 1) 
Overlay oluşturalım
Ana dizine (/home/pi/) overlay dosyasını mkstft35_rpi.dts kopyalayalım [DTS Dosyalarını indirin](https://github.com/technical50/TFT35V1.0-Klipper-screen/raw/main/DTS.rar).
ve konsola aşağıdaki komut satırını girelim (overlay derleme):
```shell
sudo dtc -@ -I dts -O dtb -o /boot/overlays/mkstft35_rpi.dtbo ~/mkstft35_rpi.dts
```

konsolda şöyle birşey görmeniz gerekir:

![main](https://github.com/technical50/TFT35V1.0-Klipper-screen/blob/main/pictures/overlayı.PNG) 


# 2) 
Overlay için şu dosyayı düzeltelim
/boot/config.txt
```shell
sudo nano /boot/config.txt
```
ekran overlay dosyasına bağlanıp
aşağıdaki satırları config /boot/config.txt dosyaya ekleyelim (spi ayarının altına eklemeniz tavsiye olunur):
```shell
###### MKS TFT35
hdmi_force_hotplug=1
hdmi_cvt=hdmi_cvt=480 320 60 1 0 0 0
hdmi_group=2
hdmi_mode=1
hdmi_mode=87
display_rotate=0


dtoverlay=mkstft35_rpi,rotate=270,speed=24000000,touch,touchgpio=17,fps=20
###### MKS TFT35 for 180 degree rotate please write ''rotate=270''
```

Kaydet için (Ctrl+S) ve nano düzenleyiciden çık (Ctrl+X).

Moonraker ile bağlantı kopma sorunu varsa!!!
aşağıdaki satırları config.txt dosyasında bulup önüne # koyarak deaktif hale getirelim!

```shell
# Enable DRM VC4 V3D driver
#dtoverlay=vc4-kms-v3d
#max_framebuffers=2

# Disable compensation for displays with overscan
#disable_overscan=1
```

Önemli bilgi!
ADXL bağlantısını yapamayacaksınız SPI 0 bağlantısı ekran-kart arası bilgi transferi için kullanılıyor. Bunun için birkaç önerimiz var
Bu kart ile [MPU6050]( https://www.klipper3d.org/Measuring_Resonances.html) sensör bağlantı tipi I2C portu ile bağlanmak. Ayrıca ADLX345 sensörü için harici MCU bağlantısı yaparak onun SPI portunu kullanmak Örneğin=[Robin Nano](https://www.reddit.com/r/klippers/comments/ul5h6p/accelerometer_adxl345_wired_to_robin_nano_v1x/), [Nano](https://nate15329.com/klipper-input-shaper-w-arduino-nano/), [Pico](https://klipper.discourse.group/t/raspberry-pi-pico-adxl345-portable-resonance-measurement/1757) ...
6050 daha ucuz olabilir.

# 3) 
FBCP Kurulumu
gerekli dosyaları kopyalayarak birincil framebufferi ikincil olarak atama.
Sıra ile komutları konsoldan giriş yapın:
```shell
sudo apt-get install cmake
cd ~
sudo git clone https://github.com/technical50/rpi-fbcp
cd rpi-fbcp/
sudo mkdir build
cd build
sudo cmake ..
sudo make
sudo install fbcp /usr/local/bin/fbcp
```
Servis dosyasını oluşturma:
```shell
sudo nano /etc/systemd/system/fbcp.service
```
İçeriğine aşağıdaki satırları ekleyin.:
```shell
[Unit]
Description=fbcp
After=KlipperScreen.service
StartLimitIntervalSec=0
[Service]
Type=simple
Restart=always
RestartSec=1
User=root
ExecStart=/usr/local/bin/fbcp

[Install]
WantedBy=multi-user.target
```
Kaydet (Ctrl+S) Nano düzenleyiciden çık (Ctrl+X).

Servisin çalışmasını sağlayalım:
```shell
sudo systemctl enable fbcp.service
```
```shell
sudo reboot now
```
"Önemli bilgi!!!"

Eğer Rasperry pi de voltaj düşüklüğü algılanıyorsa KlipperScreen yeniden başlatma sonrası açılmayacak ekranda sol köşede - gözükecektir,
Google dan Rasperry pi için düşük voltajı nasıl çözerim kısmını araştırıp bu sorunu atlatmayı deneyin.

# 4)

Ekran kalibrasyonu yapmak için gerekli dosyaları yüklemek gerekli:

Kurulum [xinput-calibrator](https://github.com/technical50/xlibinput_calibrator)
```shell
sudo apt install libxi-dev libx11-dev libxrandr-dev
```
kurulum ve derlemeyi sağlayın:
```shell
git clone https://github.com/technical50/xlibinput_calibrator.git
cd xlibinput_calibrator/src/
make
ls -l xlibinput_calibrator
```
Şimdi" ~/xlibinput_calibrator/src/" klasörü içinde iken kalibrasyon komutunu çalıştıralım:

```shell
DISPLAY=:0 ./xlibinput_calibrator --output-file-x11-config=x11_config.txt
```
Şimdi ekranda kalibrasyon belirmesi lazım ve + lara bir dokunmatik kalemi ile basarak her köşeden kalibrasyon yapalım.. İşlem bitince Konsola aşağıdaki gibi bir yazı belirecektir.

![main](https://github.com/technical50/TFT35V1.0-Klipper-screen/blob/main/pictures/cabrator.PNG)

Sonuçları aşağıdaki dosyayı oluşturup içine kopyalayalım:

```shell
sudo nano /usr/share/X11/xorg.conf.d/99-calibration.conf
```
Ctrl+S Kaydet  Ctrl+X Çıkış yap
Örneğin benim parametlerim böyleydi:
```shell
Section "InputClass"
	Identifier	"calibration"
	MatchProduct	"ADS7846 Touchscreen"
	Option		"CalibrationMatrix"	"-0.002508 1.113676 -0.059933 -1.082690 -0.008377 1.046131 0.000000 0.000000 1.000000 "
EndSection
```
Yeniden başlat
```shell
sudo reboot now
```


Ekran düzenli olarak çalışmalı Hayırlı olsun.



Boştaki pinler 3,5,7,8,9,10,13,14,16,17.Gri pinler meşgul olan pinler ihtiyacınıza göre kullanmak istedikleriniz için buraya bilgi olsun diye koyduk.
![main](https://github.com/technical50/TFT35V1.0-Klipper-screen/blob/main/pinmap.png)

# MKS TFT35 marlin gibi "M300: Ton çaldır"

MKS TFT 35 de aktif bir buzzer var bunu klipper makroları ile kullanabilirsiniz.

Rasppery için mikro kontrolcü bağlantısı zaten sahipsiniz eğer bağlantınız yooksa nasıl yapılacağına göz atın: [RPi Microcontroller service as described in Klipper documentation](https://www.klipper3d.org/RPi_microcontroller.html)

Bu satırları `printer.cfg` ye ekleyin:
```
[mcu rpi]
serial: /tmp/klipper_host_mcu

[output_pin BEEPER_Pin]
pin: rpi:gpio22 # for Raspberry Pi
pwm: True
```

Bu makro yu da `macros.cfg`dosyasına kaydedin [Source](https://www.reddit.com/r/klippers/comments/o775te/create_marlin_like_m300_beeper_tone/).
```
[gcode_macro M300]
gcode:  
    {% set S = params.S | default(1000) | int %} ; S sets the tone frequency
    {% set P = params.P | default(100) | int %} ; P sets the tone duration
    {% set L = 0.5 %} ; L varies the PWM on time, close to 0 or 1 the tone gets a bit quieter. 0.5 is a symmetric waveform
    {% if S <= 0 %} ; dont divide through zero
    {% set F = 1 %}
    {% set L = 0 %}
    {% elif S >= 10000 %} ;max frequency set to 10kHz
    {% set F = 0 %}
    {% else %}
    {% set F = 1/S %} ;convert frequency to seconds 
    {% endif %}
    SET_PIN PIN=BEEPER_Pin VALUE={L} CYCLE_TIME={F} ;Play tone
    G4 P{P} ;tone duration
    SET_PIN PIN=BEEPER_Pin VALUE=0

[gcode_macro BUZZER_TEST]
gcode:
    M300 S1000 P100
    M300 S1000 P100
    M300 S1000 P100
```
Kaydedip yeniden başlatarak  `BUZZER_TEST` makrosu ile test yapabilirsiniz.
