---
title: DIY Potentiostat Menggunakan LabVIEW dan Arduino Uno

---

# DIY Potentiostat Menggunakan LabVIEW dan Arduino Uno
### Oleh Muhammad Pirel, 2025

Sekitar satu semester yang lalu, saat saya sedang browsing journal kimia (saat itu sedang riset cara sintesis asam nitrat langsung dari udara, menggunakan metode elektrokimia, tanpa memerlukan setup Haber-Bosch atau Birkeland-Eyde) saya tidak sengaja menemukan jurnal ini : https://pubs.acs.org/doi/10.1021/acs.jchemed.8b00340. 

![image](https://hackmd.io/_uploads/rkQQN7A7bl.png)

Kebetulan saja saat itu saya juga sedang mencari cari cara untuk mendapatkan atau membuat potentiostat dengan murah. Bagi yang belum mendalami, potentiostat adalah instrumen laboratorium elektrokimia yang berguna untuk melakukan analisis kinetik dari reaksi redox pada suatu larutan, seperti *Cyclic Voltammetry* dan *Chronoamperemetry*. Niat saya dengan alat ini adalah untuk melakukan analisis kinetik saya sendiri terhadap bahan bahan kimia yang saya miliki. Alasan saya tidak mencari saja data ini dari buku referensi seperti dari publikasinya *CRC Press* adalah bahan bahan kimia yang saya miliki dan dapat saya beli sering kali bukan yang golongan *analytical grade*, sebab saya sering mendapatkannya dari toko bangunan dan toko parfum (di Indonesia adanya hanya toko seperti itu wkwkw). Ini mengharuskan saya mencari data data saya sendiri jika ingin melanjutkan hobi saya dengan suplai bahan kimia yang ada di Indonesia. Tetapi selain itu, saya memang ingin memiliki potentiostat saya sendiri, terlepas dari kegunaannya.

Potentiostat yang dipaparkan di journal ini sangatlah sederhana. Dirinya terdiri dari *Arduino Uno*, yang akan berinterface dengan *Virtual Instrument LabVIEW* (ini disediakan oleh penulis jurnal) dengan bantuan firmware *Diligent Linx*, juga satu *Daughterboard* yang memiliki beberapa amplifier, integrator, current mirror, dan *low pass filter*. Tujuan dari *Daughterboard* nya sendiri adalah untuk menerima sinyal pwm dari arduino uno dan menjadikannya suatu nilai tegangan pada *Working Electrode* dan *Counter Electrode*, relatif terhadap *Reference Electrode*. Saat beroperasi nanti, *Arduino Uno* akan dikendalikan oleh Virtual Instrument yang ada di laptop kita untuk mengirim dan menerima sinyal dari *Daughterboard*, untuk mana setiap sinyal yang diolah oleh *Daughterboard* akan berkorelasi dengan nilai tegangan di ketiga electrode tadi.

![image](https://hackmd.io/_uploads/BkQ9n7C7Ze.png)
Gambar referensi potentiostat dari jurnal tersebut

## Proses Pembuatan Daughterboard

Proses ini dimulai dari pencetakan PCB yang diperlukan. Jurnal tersebut memiliki supporting information yang berisi file file yang diperlukan untuk memanufaktur alat potentiostat ini: .stl file untuk 3d printing casing dan .gbr untuk cetak pcb. Saya pribadi tidak menggunakan casing 3d printing yang diberikan, melainkan saya gunakan box elektrikal standar dengan ukuran X5.

Untuk pencetakan PCBnya saya menggunakan jasa cetak pcb yang ada di Shopee, spesifiknya adalah vendor RaftechPCB. Saya mengirimkan ke vendor file .gbr yang front layer saja. Kemudian, saya set di website RaftechPCB agar dicetak di bahan FR4, Single Layer, Tanpa Soldermask dan Silkscreen, dengan ukuran 60x60mm. Untuk 3 keping PCB, saya membayar 62000 rupiah. 

![WhatsApp Image 2025-12-28 at 11.31.36](https://hackmd.io/_uploads/ryCTGVRXWx.jpg)

Untuk komponen komponen elektronikanya sendiri adalah sebagai berikut

![image](https://hackmd.io/_uploads/SJ-7Q4CXWe.png)

harga total dari komponen elektronikanya (untuk 3 board) adalah sekitar 29500 rupiah.

![image](https://hackmd.io/_uploads/SyJcBEC7Wl.png)

Berikut adalah pcb yang sudah tercetak beserta komponen komponennya

![WhatsApp Image 2025-12-25 at 23.45.40](https://hackmd.io/_uploads/H1hArVAQZe.jpg)

Komponen komponen ini kemudian akan disolder ke pcb yang sudah dicetak menggunakan solder deko 40 watt, timah solder 63/37 diameter 0,5mm, dan flux RMA 223. Alat alat tambahan lain yang diperlukan dalam proses ini adalah tang potong dan tang cucut. Tata letak komponen saya dapatkan dari dokumentasi / instruksi yang diberikan oleh tim penulis jurnal.

![image](https://hackmd.io/_uploads/ryDdINC7bx.png)
![image](https://hackmd.io/_uploads/H1QsLNAQbl.png)

Setelah semua disolder, jadinya adalah seperti ini (Catatan: saya lupa melakukan mirroring terhadap file gerber saya. Oleh sebab itu, letak copper trace dan komponen ada di satu layer. Cara saya mengakali ini adalah dengan memberikan elevasi pada komponen dan kaki yang ingin disolder, sehingga penyolderan lebih mudah).

![WhatsApp Image 2025-12-26 at 01.56.37](https://hackmd.io/_uploads/rkvfwV0XWg.jpg)
![WhatsApp Image 2025-12-26 at 01.56.57](https://hackmd.io/_uploads/Sy-7PN0XZx.jpg)

Untuk interfacing ke Arduino dan 3 Electrodenya, saya pasang kabel 22 awg padat (untuk Arduino) dan kabel 22 awg serabut + jepitan buaya (untuk electrode). Tips dari saya adalah, jika tidak menemukan kabel 22 awg padat, beli saja kabel telepon, isinya sama saja, kabel padat 22 awg. 

![image](https://hackmd.io/_uploads/S1dfdVRX-x.png)


## Proses Pemasangan Firmware ke Arduino Uno


Setelah motherboard siap, tahap berikutnya adalah memasang firmware Linx ke arduino uno agar bisa berinterface dengan Virtual Instrument yang ada di laptop kita.

Tahap pertama adalah menginstall LabVIEW. Versi dari LabVIEW yang didownload harus lebih awal dari tahun 2018, ini disebabkan beberapa fitur seperti Linx Firmware Wizard hanya ada di versi LabVIEW yang terbaru. Dan untuk lisensi, install saja yang versi evaluasi, LabVIEW ini hanya akan digunakan untuk memasang firmware Linx ke Arduino, saat operasi nanti akan digunakan software VI yang telah disediakan.

Setelah diinstall, harus juga memasang aplikasi Virtual Instrument Package Manager. Tugasnya adalah untuk memasang package Diligent Linx. Jika selesai dan berhasil, hasilnya adalah sebagai berikut:

![WhatsApp Image 2025-12-27 at 11.01.47](https://hackmd.io/_uploads/r1xX0NAQZg.jpg)

Setalah itu, bukalah VI Linx Firmware Wizard, yang akan memunculkan window seperti berikut:

![WhatsApp Image 2025-12-27 at 11.03.24](https://hackmd.io/_uploads/BJCtCNC7-e.jpg)

Silahkan atur wizardnya agar berinterface dengan Arduino Uno menggunakan Serial / USB.

Tahap berikutnya adalah menghubungkan Arduino Uno ke laptop. Pastikan Serial Interface CH340 Arduino Uno terbaca oleh com port laptop, gunakan Device Manager (jika pada Windows) untuk troubleshooting. Jika ada kendala, coba rollback driver untuk CH340 ke versi tahun 2019, ini sebab beberapa batch dari chip CH340 yang terpasang di Arduino memiliki arsitektur yang berbeda dan sudah mengalami depresiasi, sehingga tidak didukung driver terbaru.

![WhatsApp Image 2025-12-27 at 11.05.21](https://hackmd.io/_uploads/S1EPyr0mbl.jpg)

Setelah itu pasang firmware ke arduino, ikuti seperti gambar dibawah ini

![WhatsApp Image 2025-12-27 at 11.07.26](https://hackmd.io/_uploads/B1j4erRm-g.jpg)

Jika sudah, buka Arduino IDE. Selain memasang firmware Linx, kita juga harus mengubah frequency PWM Arduino Uno yang kita pakai, disebabkan kecepatan respon dan stabilitas potentiostat kita bergantung pada kecepatan PWM yang tinggi (32 khz). Upload code berikut ke Arduino Uno:

```clike=
#include <SPI.h> 
#include <Wire.h> 
#include <EEPROM.h> 
#include <Servo.h> 
#include <LinxArduinoUno.h> 
#include <LinxSerialListener.h> 
LinxArduinoUno* LinxDevice; 
void setup() 
{ 
LinxDevice = new LinxArduinoUno(); 
LinxSerialConnection.Start(LinxDevice, 0); 
TCCR1B =B00000001;  
} 
void loop() 
{ 
LinxSerialConnection.CheckForCommands();  
} 

```
![WhatsApp Image 2025-12-27 at 11.36.51](https://hackmd.io/_uploads/B1UlWB0XZl.jpg)

Untuk bisa compile, diperlukan library dari https://github.com/LVMakerHub/LINX/tree/main/LabVIEW/vi.lib/MakerHub/LINX/Firmware/Source/libraries. Ambil folder LinxArduinoUno dan LinxSerialReader dan install ke Arduino IDE anda.

![image](https://hackmd.io/_uploads/HkRmfBCm-e.png)

Setelah firmware dan code diupload ke Arduino Uno, buka aplikasi JUAMI_Potentiostat.exe yang telah diberikan pada supporting information. Pilih port com yang benar, dan klik start. Jika semua tahap sebelumnya berhasil dan arduino tersambung dengan laptop, akan muncul plotting *Cyclic Voltammetry* seperti berikut (hasil anda akan berbeda dengan saya, ini sebab belum ada sel elektrokimia yang dipakai, pada dasarnya ketiga elektroda di open circuitkan).

![image](https://hackmd.io/_uploads/BJsJmrCm-e.png)

## Testing dan Assembly

Sambungkan kabel dari Daughterboard ke Arduino Uno yang telah diinstall firmware tadi. Informasi wiring ada di dokumen instruksi yang diberikan oleh penulis jurnal. Untuk sel elektrokimia testing, gunakan air garam sebagai elektrolit dan kawat timah solder sebagai ketiga electrodenya. Pasang, dan jalankan aplikasinya

![WhatsApp Image 2025-12-27 at 22.12.06](https://hackmd.io/_uploads/B1W5XSR7bl.jpg)

Jika sudah berhasil, rakitlah ke box electrixal ukuran X5 seperti berikut (gunakan lem tembak untuk perekat dan bahan potting)

![WhatsApp Image 2025-12-28 at 06.18.41](https://hackmd.io/_uploads/BkFAXBC7Wl.jpg)

## Hasil

![WhatsApp Image 2025-12-28 at 06.33.10](https://hackmd.io/_uploads/BJMSESRX-g.jpg)
![WhatsApp Image 2025-12-28 at 06.49.41](https://hackmd.io/_uploads/H14INH07-e.jpg)

## Kesimpulan

Potentiostat ini memiliki desain yang sederhana dan mudah diterapkan, memanfaatkan komponen komponen yang umum. Selain itu, karena software softwarenya sudah diberikan, maka kita tidak perlu melakukan programming lagi. Total harga dari alat ini adalah sekitar 120 ribu rupiah, mengasumsikan 10000 adalah harga komponen elektronika, 20000 harga cetak satu keping pcb, 70000 harga Arduino Uno, 10000 harga box electrical, dan 10000 sisanya adalah harga kabel dan jepitan buaya. Jika anda sudah berpengalaman dengan software LabVIEW, maka alat ini dapat dikerjakan pada satu hari penuh. Karena harganya murah dan perakitan yang cepat, saya sarankan anda yang memiliki hobby kimia amatir menco merakit alat ini, walaupun anda masing amatir di bidang elektronika. Selain itu, alat ini bisa juga dijual, menjadikan projek ini dasar dari bisnis rumahan yang bisa anda coba.

Terima kasih
Muhammad Pirel

