# Aplikasi Web "HomeBox"


## Sekilas Tentang

HomeBox adalah sebuah aplikasi web berbasis self-hosted yang digunakan untuk melakukan inventarisasi dan manajemen aset secara terpusat. Aplikasi ini memungkinkan pengguna untuk mencatat, mengelompokkan, dan memantau berbagai barang yang dimiliki, baik untuk keperluan pribadi, rumah tangga, maupun organisasi. Beberapa fitur yang dimiliki oleh HomeBox adalah sebagai berikut:
1. Pencatatan data aset, meliputi nama, deskripsi, kategori, lokasi penyimpanan, dan kondisi barang.
2. Manajemen kategori dan lokasi, yang memudahkan pengelompokan barang sesuai jenis atau tempat penyimpanannya.
3. Dukungan unggah gambar, sehingga setiap item dapat disertai foto sebagai identifikasi visual.
4. Pencarian dan penyaringan data, untuk mempermudah pengguna dalam menemukan barang tertentu.
5. Akses berbasis web, sehingga dapat dijalankan melalui peramban (browser) tanpa memerlukan instalasi tambahan di sisi pengguna.

## Instalasi

**Persiapan :**
- Sistem operasi: Windows / macOS / Linux (disarankan Ubuntu 22.04).

- Perangkat lunak: Docker & Docker Compose.

- Akses: Terminal/CLI dan koneksi internet.

- Server: Hostinger VPS dengan akses root/SSH aktif dan port 3100 terbuka.

- Spesifikasi : Ram 2 GB+ (minimum 1 GB, tapi rawan swap kalau banyak container), Storage 10 GB+ SSD.

- (Opsional): FileZilla, WinSCP (untuk transfer file ke server), Docker Compose file (.yml)

**1. Download Homebox**

1. Buka halaman GitHub Homebox: https://github.com/sysadminsmedia/homebox. 
2. Klik tombol "Code" lalu klik "Download ZIP" untuk mengunduh folder ZIP homebox.
<img width="1897" height="869" alt="image" src="https://github.com/user-attachments/assets/8ed24459-1a61-44ab-9f95-019847962b3b" />

3. Ekstrak file ZIP tersebut di komputer kamu (misal di folder Downloads/homebox/).

**2. Install Docker**

**Opsi 1:** Download Docker Desktop dari situs resminya (fitur GUI).

- Unduh Docker Desktop dari https://www.docker.com/products/docker-desktop/.

- Pilih sistem operasi yang didukung.
<img width="1896" height="823" alt="image" src="https://github.com/user-attachments/assets/d4866863-7ff6-4ad6-a9c4-bbe8fd9acac2" />

- Jalankan instalasi, lalu buka Docker Desktop setelah selesai.

**Opsi 2:** Install langsung dari terminal:

```sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable docker
sudo systemctl start docker
```

Cek apakah Docker sudah aktif:

```
docker --version
docker compose version
```

**4. Buat Folder Data Homebox**

Buka terminal (atau PowerShell) lalu jalankan:

```
mkdir -p ~/homebox-data
```

**5. Buat File docker-compose.yml**

Masuk ke folder hasil ekstrak Homebox, lalu buat file bernama docker-compose.yml dan isi dengan:

```yaml
version:
services:
  homebox:
    image: ghcr.io/sysadminsmedia/homebox:latest
    container_name: homebox
    restart: unless-stopped
    ports:
      - "7745:7745"
    volumes:
      - ./data:/data
    environment:
      - TZ=Asia/Jakarta
      - HBOX_LOG_LEVEL=info
      - HBOX_LOG_FORMAT=text
      - HBOX_WEB_MAX_UPLOAD_SIZE=25
      - HBOX_OPTIONS_ALLOW_REGISTRATION=true
      - HBOX_WEB_READ_TIMEOUT=15s
      - HBOX_WEB_WRITE_TIMEOUT=15s
      - HBOX_WEB_IDLE_TIMEOUT=60s
      - HBOX_THUMBNAIL_ENABLED=true
      - HBOX_THUMBNAIL_WIDTH=400
      - HBOX_THUMBNAIL_HEIGHT=400
      - HBOX_OPTIONS_GITHUB_RELEASE_CHECK=true
```

**6. Jalankan Aplikasi dan Testing Secara Lokal**

1. Masih di folder tempat file docker-compose.yml disimpan, jalankan:

```
docker compose up -d --build
```

2. Cek apakah container aktif:

```
docker ps
```

3. Jika homebox muncul → akses melalui: http://localhost:3100/.

**7. Deploy ke Server**

1. Login ke VPS:
```
ssh root@<IP_SERVER>
```
> Keterangan:
> - Di Hostinger, kamu bisa melihat IP server di HPanel → VPS → Dashboard → “Server details” / “IP Address”.
Contoh: 145.xx.xx.x
> - Username default biasanya root (kecuali kamu buat user lain).*

2. Masukkan Password VPS.

3. Update & Install Docker:
```
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io docker-compose -y
sudo systemctl enable docker
sudo systemctl start docker
```
4. Upload Folder Homebox ke Server

- Kamu bisa unggah folder proyek ke server Hostinger lewat File Manager di hPanel, SSH langsung, atau FTP/SFTP (FileZilla/WinSCP). Pastikan folder ada di /root/homebox/ sebelum lanjut ke proses build Docker.

  Misalnya, mengunggah folder ke server via SSH dengan cara :
```
scp -r <path_folder_lokal> <username>@<ip_server>:<path_tujuan_di_server>
```

- Setelah selesai, masuk ke server melalui SSH. Lalu cek hasil unggahan:
```
ls /root/homebox
```

5. Build dan Jalankan:
```
docker compose up -d --build
```

6. Cek Container:
```
docker ps
```

7. (Opsional) Izinkan Firewall:
```
sudo ufw allow 3100/tcp
sudo ufw reload
```

**7. Akses Web App**

1. Buka browser, lalu ketik:

```
http://<IP_SERVER>:xxxx
```

Contoh:
```
http://145.79.13.5:7745
```

## Konfigurasi

Variable dan configuration environment yang dapat diatur untuk aplikasi HomeBox tersebut dapat dilihat pada: https://homebox.software/en/configure/. Adapun penjelasan untuk pengaturan tersebut adalah sebagai berikut:

Berdasarkan isi dari file `docker-compose.yml`

```
TZ=Asia/Jakarta
```
Menetapkan zona waktu server ke Waktu Indonesia Barat (WIB) agar memastikan timestamp pada semua data, log, dan catatan aktivitas di aplikasi HomeBox akurat sesuai waktu lokal.
```
HBOX_LOG_LEVEL=info
```
Mengatur tingkat logging yang efisien, menyeimbangkan detail dan penggunaan disk.
```
HBOX_LOG_FORMAT=text
```
Menentukan format output dari log aplikasi sehingga mudah dibaca dan dianalisis secara langsung di terminal.
```
HBOX_WEB_MAX_UPLOAD_SIZE=25
```
Membatasi ukuran file upload maksimum menjadi 25 MB, menghemat ruang disk.
```
HBOX_OPTIONS_ALLOW_REGISTRATION=true
```
Mengizinkan pendaftaran akun oleh publik.
```
HBOX_WEB_READ_TIMEOUT=15s
HBOX_WEB_WRITE_TIMEOUT=15s
HBOX_WEB_IDLE_TIMEOUT=60s
```
Meningkatkan stabilitas dengan memutus koneksi klien yang lambat membaca/mengirim permintaan setelah 15 detik dan juga membebaskan resource server dengan menutup koneksi yang tidak aktif (idle) setelah 60 detik.
```
HBOX_THUMBNAIL_ENABLED=true
HBOX_THUMBNAIL_WIDTH=400
HBOX_THUMBNAIL_HEIGHT=400
```
Mengaktifkan pembuatan thumbnail untuk gambar, meningkatkan UX dan kecepatan loading dengan menetapkan resolusi thumbnail sebesar 400x400 pixel.
```
HBOX_OPTIONS_GITHUB_RELEASE_CHECK=true
```
Memberi notifikasi otomatis di web interface jika ada pembaruan versi baru.

##  Maintenance (opsional)

Setting tambahan untuk maintenance secara periodik, misalnya:
- buat backup database tiap pekan
- hapus direktori sampah tiap hari
- dll

## Otomatisasi (opsional)

Skrip shell untuk otomatisasi instalasi, konfigurasi, dan maintenance.

**1. Instalasi Docker atau Melakukan Pengecekan Instalasi Docker**

```
if ! command -v docker &> /dev/null
then
    echo "Docker belum terinstal."
    sudo apt update
    sudo apt install -y docker.io docker-compose
    sudo usermod -aG docker $(whoami)
    echo "Keluar dan masuk kembali ke SSH agar 'docker' aktif!"
else
    echo "Docker dan Docker Compose sudah terinstal."
fi

mkdir -p $HOST_DIR
cd $HOST_DIR
```

**2. Konfigurasi**

Konfigurasi dapat dilakukan dengan kode sebelumnya, pada sub bab "konfigurasi". 

**3. Membersihkan kontainer, volume, dan jaringan docker yang tidak digunakan**
```
docker system prune -a -v -f
```

**4. Membersihkan images docker yang tidak digunakan**
```
docker image prune -a -f
```


## Cara Pemakaian

### Akses Aplikasi
- Buka browser, lalu masuk ke alamat server HomeBox.
  [HomeBox Kelompok 6](http://145.79.13.5:7745).
  <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/bcbb6c9f-7f28-429f-a633-abdcdbd5a848" />
  
### Login
- Masukkan *username* dan *password* yang sudah terdaftar.
  <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/8dd4c65d-33a6-491e-ad11-1337db5fc1f4" />
- Setelah berhasil login, pengguna akan diarahkan ke halaman Beranda.
  
### Beranda (Dashboard)
- Menampilkan ringkasan data: *Total Nilai Barang, Jumlah Item, Jumlah Lokasi, dan Jumlah Label*.
  <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ad54725-d9f8-4924-88c3-cc8fcfe672fb" />
- Tombol **"+ Buat"** digunakan untuk menambah item baru.
  <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/9cf700b2-1d56-4f9d-a341-2c849b9d7792" />
  
### Menambahkan Item Baru
- Klik tombol **"+ Buat"**, lalu pilih **"Item/Aset"**.
- Isi semua kolom yang tersedia:
  - **Lokasi Induk** -> pilih lokasi penyimpanan barang.
  - **Nama Item** -> isi nama barang.
  - **Item Quantity** -> jumlah barang.
  - **Deskripsi Item** -> keterangan barang.
  - **Label** -> pilih kategori barang.
  - **Item Photo** -> unggah foto barang (opsional).
- Gunakan salah satu tombol di bawah form:
  - **Buat** -> simpan dan tutup form.
  - **Buat dan Tambah Baru** -> simpan dan lanjut isi data baru.
  - **Shift + Enter** -> shortcut keyboard untuk tambah data cepat.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/4440be7c-4bca-41a3-b61d-6e35edf92943" />

### Menambahkan Lokasi Baru
- Klik tombol **"+ Buat"**, lalu pilih **"Lokasi"**.
- Isi kolom:
  - **Lokasi Induk** -> jika lokasi berada di dalam lokasi lain (opsional).
  - **Nama Lokasi** -> isi nama lokasi, contoh: *Bedroom*, *Office*.
  - **Deskripsi Lokasi** -> keterangan lokasi (opsional).
- Klik tombol **Buat** atau **Buat dan Tambah Baru** untuk menyimpan.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/27f581e4-cb68-4835-8737-82ece919e7a9" />

### Menambahkan Label Baru
- Klik tombol **"+ Buat"**, lalu pilih **"Label"**.
- Isi data:
  - **Nama Label** -> isi nama label, contoh: *Electronics*.
  - **Deskripsi / Keterangan Label** -> keterangan label (opsional).
  - **Label Color** -> pilih warna label agar mudah dibedakan.
- Klik **Buat** untuk menyimpan label baru.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/b72e3b01-9f6f-4be9-9474-93e6bb1f5d44" />

### Melihat & Mengelola Barang di Setiap Lokasi
- Setelah membuat lokasi, semua daftar tempat penyimpanan akan tampil di menu **"Lokasi"**.
  <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/137195bf-6d15-4e2f-bca6-d832eb25536a" />
- Tiap Lokasi bisa diklik untuk melihat atau menambahkan item di dalamnya.
- Setelah memilih salah satu lokasi (misal *Bedroom*), pengguna akan diarahkan ke halaman berisi daftar barang yang tersimpan di lokasi tersebut.
- Setiap item ditampilkan dalam bentuk kartu berisi **nama, deskripsi, label, dan foto barang**.
- Di bagian atas halaman terdapat tombol:
  - **Labels** -> untuk mengatur label atau kategori barang di lokasi tersebut.
  - **Sunting** -> untuk mengedit detail lokasi seperti nama dan deskripsi.
  - **Hapus** -> untuk menghapus lokasi beserta isinya.
  - **Kartu / Tabel** -> untuk mengganti tampilan daftar barang (mode kartu / tabel).
- Fitur ini memudahkan pengguna melacak dan mengatur barang berdasarkan lokasi penyimpanan.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/defb85b6-5a15-49a6-bc32-ee771df66143" />

### Melihat dan Mengelola Detail Barang
- Klik salah satu item di dalam lokasi (misal *Tempat Tidur* di *Bedroom*) untuk melihat detailnya.
- Halaman ini menampilkan informasi lengkap seperti:
  **Nama Barang**, **Lokasi**, **Label**, dan **Deskripsi**.
- Di bawah deskripsi terdapat 3 tab utama:
  - **Detail** -> menampilkan informasi rinci seperti jumlah barang, nomor seri, model, hingga harga (jika diisi).
    <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/931b0e96-1bc2-4b33-9b0b-b0f80895e1cc" />
  - **Perbaikan** -> menampilkan daftar kegiatan pemeliharaan yang pernah dilakukan terhadap barang tersebut.
    <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/91bcb679-eef1-41ca-a71c-c54530cb6ac5" />
  - **Sunting** -> digunakan untuk mengedit data barang seperti nama, deskripsi, jumlah, lokasi, dan label.
    <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/6a415e50-2d28-4330-851c-bf5e5d97f6e8" />
    - Saat membuka tab **Sunting**, pengguna bisa memperbarui informasi dasar barang.
    - Jika mengaktifkan opsi **Tingkat Lanjut**, akan muncul kolom tambahan yang bisa diisi, seperti:
      - **Biaya Pembelian** -> harga saat barang dibeli.
      - **Rincian Garansi** -> masa garansi dan informasi penyedia garansi.
      - **Detail Penjualan** -> data transaksi jika barang dijual kembali.
      <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/54e155da-c5b6-415e-9c41-bcde8fe2aa71" />
      <img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/13f588de-7ca7-448f-b9f2-cb9ca195e15a" />
- Pada bagian atas juga tersedia tombol:
  - **Labels** -> melihat label yang digunakan untuk item tersebut.
  - **Create Subitem** -> menambahkan barang turunan (misal *Bantal*).
  - **Duplikat** -> membuat salinan item dengan data yang sama.
  - **Hapus** -> menghapus item dari daftar.

### Mencari Barang
- Gunakan kolom **"Cari"** di bagian atas halaman untuk menemukan barang berdasarkan nama, label, atau lokasi.
- Ketik kata kunci dan hasil akan langsung ditampilkan.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/d2b4f761-3c58-4170-9ed1-60dc02c78f39" />

### Pemeliharaan Barang
- Fitur **Pemeliharaan** digunakan untuk mencatat dan memantau kegiatan perawatan atau perbaikan barang.
- Untuk menambahkan pemeliharaan baru:
  1. Buka halaman **Detail Barang**.
  2. Klik tab **Perbaikan**.
  3. Tekan tombol **+ Baru** di pojok kanan atas.
- Isi data yang tersedia:
  - **Nama Entri** -> nama kegiatan perawatan (misal: *Ganti sprei*).
  - **Tanggal Terjadwal** -> jadwal perawatan direncanakan.
  - **Tanggal Selesai** -> waktu perawatan selesai dilakukan.
  - **Catatan** -> deskripsi detail kegiatan.
  - **Biaya** -> nominal biaya jika ada pengeluaran.
- Setelah disimpan, daftar pemeliharaan akan muncul di tab **Pemeliharaan**.
- Fitur ini membantu pengguna melacak kondisi dan riwayat perawatan barang dengan mudah.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/28e34c66-295f-4d6f-87d4-34f528e5f209" />

### Profil dan Pengaturan
- Buka menu **Profil** di sidebar kiri untuk melihat informasi akun.
- Pengguna bisa memperbarui nama atau kata sandi bila diperlukan.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/da757803-bf19-48fb-b961-c341c503b6ea" />

### Tools (Laporan & Utilitas)
- Menu **Tools** berfungsi untuk memmbantu pengguna dalam mengelola data inventaris secara lebih luas.
- Di dalam menu ini terdapat beberapa fitur pendukung seperti:
  - **Laporan Inventaris** -> menampilkan rekapitulasi barang berdasarkan lokasi, label, atau kategori tertentu.
  - **Ekspor Data** -> memungkinkan pengguna mengunduh data inventaris dalam format tertentu (misal CSV atau JSON) untuk keperluan backup atau analisis.
  - **Impor Data** -> digunakan untuk menambahkan data barang secara massal dari file yang sudah ada.
- Fitur ini sangat berguna untuk pengelolaan data dalam jumlah besar atau saat pengguna ingin membuat arsip data HomeBox secara lokal.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/32ec1f8c-e8d3-4baa-991c-3aa7232a378b" />

### Logout
- Setelah selesai menggunakan aplikasi, klik **"Keluar"** di bagian bawah sidebar kiri.
- Langkah ini penting untuk menjaga keamanan akun.
<img width="800" height="1080" alt="image" src="https://github.com/user-attachments/assets/7718e8eb-4438-4ba5-bd62-4661a2005613" />


## Pembahasan

### Informasi Terkait Homebox
Homebox adalah aplikasi web self-hosted berbasis open source yang berfungsi untuk mengelola dan mendokumentasikan inventori pribadi. Aplikasi ini memungkinkan pengguna menyimpan informasi barang-barang yang dimiliki di rumah, kantor, atau gudang secara digital, sehingga mempermudah pelacakan dan pengelolaan aset.

Homebox dikembangkan oleh SysAdmins Media, dan dapat dijalankan menggunakan Docker sehingga instalasi menjadi lebih cepat, ringan, dan mudah di-maintain tanpa konfigurasi server yang rumit. Aplikasi ini berjalan sebagai layanan tunggal (single container) yang diakses melalui browser.

### Kelebihan Homebox
1. **Proses instalasi sangat mudah** : 
Homebox dapat dijalankan hanya dengan satu perintah menggunakan Docker, yaitu docker compose up -d. Pengguna tidak perlu melakukan konfigurasi server, database, maupun dependensi secara manual. Hal ini membuatnya cocok untuk pemula yang ingin mencoba aplikasi self-hosted.

2. **Tampilan antarmuka sederhana dan mudah digunakan** : 
Desain Homebox bersifat minimalis namun intuitif. Setiap fitur ditempatkan dengan rapi, sehingga pengguna baru dapat langsung memahami cara menambah dan mengelola barang tanpa perlu panduan rumit.

3. **Performa aplikasi cepat dan ringan** : 
Karena dibangun menggunakan bahasa pemrograman Golang, Homebox mampu berjalan dengan efisien bahkan pada perangkat dengan spesifikasi rendah. Aplikasi juga tetap responsif saat digunakan untuk menyimpan banyak data inventori.

4. **Fitur inti cukup lengkap untuk kebutuhan dasar** : 
Homebox sudah mencakup semua fungsi penting seperti menambah, mengedit, menghapus, dan mencari barang. Tersedia pula fitur kategori, lokasi, serta dukungan unggah gambar untuk setiap item.

5. **Dapat dijalankan di berbagai platform** : 
Karena berbasis Docker, Homebox bisa diinstal di Windows, macOS, Linux, maupun di server cloud tanpa perlu menyesuaikan sistem operasi.

### Kekurangan Homebox
1. **Membutuhkan koneksi internet saat instalasi pertama** : 
Image Docker Homebox harus diunduh dari Docker Hub. Jadi, tanpa koneksi internet, proses instalasi awal tidak bisa dilakukan.

2. **Desain antarmuka masih sederhana** : 
Walaupun mudah digunakan, tampilannya belum memiliki elemen visual modern seperti grafik statistik atau panel analisis data yang menarik.

3. **Belum mendukung ekspor data ke format lain** :
Saat ini Homebox belum menyediakan fitur bawaan untuk mengekspor data inventori ke format seperti CSV atau Excel. Backup hanya bisa dilakukan secara manual dari folder data.

4. **Dokumentasi resmi terbatas** :
Panduan instalasi dan penggunaannya di dokumentasi resmi masih singkat, sehingga pengguna mungkin perlu mencari referensi tambahan dari komunitas GitHub atau forum lain.

5. **Tidak ada sistem notifikasi atau pengingat** :
Aplikasi belum mendukung fitur seperti pengingat perawatan barang, masa garansi, atau stok menipis. Fitur yang mungkin berguna untuk inventori yang lebih kompleks.

6. **Tidak ada sistem multi peran pengguna** :
Walaupun sudah mendukung login, Homebox belum memiliki pembagian peran seperti admin dan user biasa. Semua pengguna memiliki akses yang sama terhadap data inventori.

### Perbandingan Homebox dengan aplikasi web lain yang sejenis
1. **Homebox vs Grocy**
   - **Kelebihan Homebox**: Lebih sederhana dan ringan serta tidak banyak konfigurasi kompleks. Cocok untuk penggunaan pribadi atau rumah tangga yang hanya butuh mencatat barang, kategori, lokasi, gambar, tanpa banyak fitur tambahan. Homebox juga hanya instalasi dengan Docker sehingga lebih cepat dan mudah.
   - **Kelebihan Grocy**: Fitur lebih banyak dan teliti untuk kebutuhan yang lebih kompleks. Jika membutuhkan manajemen stok bahan makanan atau alat yang perlu perawatan rutin, Grocy lebih unggul.
2. **Homebox vs InvenTree**
   - **Kelebihan Homebox**: Lebih simpel dan lebih cepat di setup serta tampilannya lebih ringan dan lebih cocok untuk penggunaan pribadi/rumah yang tidak butuh modul kompleks; pemeliharaan (maintenance) lebih mudah karena sedikit dependensi dan fungsi.
   - **Kelebihan InvenTree**: Lebih cocok jika butuh fitur lanjutan seperti pelacakan supplier dan pelacakan batch atau serial. InvenTree juga memiliki laporan yang mendalam, kontrol peran pengguna, serta integrasi lebih banyak. Jika membutuhkan inventori dengan skala besar atau butuh kontrol yang lebih presisi, InvenTree lebih powerful.


## Referensi

Cantumkan tiap sumber informasi yang anda pakai.
