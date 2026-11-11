# Project Individu Docker Compose: WordPress dan MariaDB

## 1. Deskripsi Proyek

Proyek ini merupakan implementasi Docker Compose untuk menjalankan aplikasi WordPress dengan MariaDB sebagai database terpisah.

Docker Compose digunakan untuk mengelola beberapa container dalam satu konfigurasi sehingga WordPress dan MariaDB dapat dijalankan secara bersamaan, saling terhubung, dan memiliki penyimpanan data yang persisten.

### Teknologi yang Digunakan

- **Docker Engine:** Menjalankan container.
- **Docker Compose:** Mengelola beberapa service dalam satu konfigurasi.
- **WordPress:** Aplikasi Content Management System (CMS).
- **MariaDB 11:** Database untuk menyimpan data WordPress.
- **Ubuntu:** Sistem operasi tempat Docker dijalankan.

## 2. Tujuan Proyek

Tujuan pengerjaan proyek ini adalah:

1. Memahami penggunaan Docker dan Docker Compose.
2. Menjalankan WordPress menggunakan container.
3. Menghubungkan WordPress dengan database MariaDB.
4. Memahami penggunaan environment variables.
5. Mengimplementasikan Docker volume untuk penyimpanan data.
6. Memahami komunikasi antar-container.
7. Menguji persistensi data setelah container dihentikan dan dijalankan kembali.

## 3. Struktur Direktori

Struktur direktori proyek:

```text
belajar-docker-compose-wordpres-mysql/
│
├── README.md
│
└── wordpress-compose-rama-risky/
    │
    ├── bukti-running/
    │   ├── docker-ps.png
    │   └── wordpress-dashboard.png
    │
    ├── compose.yaml
    ├── .env
    ├── .env.example
    └── .gitignore
```

### Keterangan

- `README.md`: Dokumentasi proyek dan jawaban pertanyaan eksplorasi.
- `compose.yaml`: Konfigurasi service WordPress dan MariaDB.
- `.env`: Menyimpan konfigurasi dan password yang digunakan secara lokal.
- `.env.example`: Contoh variabel lingkungan tanpa password asli.
- `.gitignore`: Mencegah file tertentu, terutama `.env`, masuk ke repository.
- `bukti-running/`: Menyimpan screenshot hasil pengerjaan dan pengujian.

## 4. Konfigurasi Docker Compose

Proyek menggunakan dua service utama:

| Service | Image | Fungsi |
|---|---|---|
| db | mariadb:11 | Menyediakan database WordPress |
| wordpress | wordpress | Menjalankan aplikasi WordPress |

### 4.1 Service Database

Service `db` menggunakan image `mariadb:11`.

Konfigurasi ini bertanggung jawab untuk:

- Membuat database WordPress.
- Membuat pengguna database.
- Mengatur password database.
- Menyimpan data database menggunakan volume `db_data`.
- Menjalankan healthcheck untuk memeriksa kesiapan database.

Healthcheck yang digunakan:

```yaml
healthcheck:
  test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
  interval: 10s
  timeout: 5s
  retries: 10
  start_period: 30s
```

Healthcheck membantu Docker memeriksa apakah MariaDB sudah siap menerima koneksi.

### 4.2 Service WordPress

Service `wordpress` menggunakan image `wordpress:latest`.

Konfigurasi ini bertanggung jawab untuk:

- Menjalankan aplikasi WordPress.
- Menghubungkan WordPress dengan MariaDB.
- Membuka aplikasi melalui port 8080.
- Menyimpan file WordPress menggunakan volume `wordpress_data`.

WordPress menggunakan konfigurasi:

```yaml
WORDPRESS_DB_HOST: db:3306
```

Artinya WordPress mengakses database melalui service bernama `db` pada port 3306.

### 4.3 Depends On

Konfigurasi yang digunakan:

```yaml
depends_on:
  db:
    condition: service_healthy
```

Konfigurasi tersebut memastikan WordPress menunggu sampai database MariaDB berstatus healthy sebelum WordPress dijalankan.

### 4.4 Volumes

Proyek menggunakan dua named volumes:

| Volume | Lokasi dalam Container | Fungsi |
|---|---|---|
| db_data | /var/lib/mysql | Menyimpan data MariaDB |
| wordpress_data | /var/www/html | Menyimpan file WordPress |

Volumes memungkinkan data tetap ada meskipun container dihentikan atau dibuat ulang.

## 5. Cara Menjalankan Proyek

### 5.1 Persiapan

Pastikan Docker Engine dan Docker Compose sudah terinstal.

Periksa versi Docker:

```bash
docker --version
```

Periksa versi Docker Compose:

```bash
docker compose version
```

### 5.2 Masuk ke Direktori Proyek

```bash
cd wordpress-compose-rama-risky
```

### 5.3 Konfigurasi Environment Variables

Isi file `.env` contoh:

```bash
cp .env.example .env
```

Edit file `.env`:

```bash
nano .env
```

Variabel yang digunakan:

```env
WORDPRESS_DB_NAME=wordpress_db
WORDPRESS_DB_USER=wordpress_user
WORDPRESS_DB_PASSWORD=isi_password_aman
MARIADB_ROOT_PASSWORD=isi_password_root_aman

WORDPRESS_PORT=8080
```

Ganti nilai password dengan password yang kuat dan jangan membagikan file `.env`.

### 5.4 Menjalankan Container

Jalankan:

```bash
docker compose up -d
```

Perintah tersebut akan membuat dan menjalankan container di background.

### 5.5 Memeriksa Container

```bash
docker compose ps
```

Pastikan container WordPress dan MariaDB berjalan.

Periksa healthcheck MariaDB:

```bash
docker inspect wordpress-db --format '{{.State.Health.Status}}'
```

Hasil yang diharapkan:

```text
healthy
```

### 5.6 Mengakses WordPress

Buka browser dan akses:

```text
http://IP-UBUNTU:8080
```

Contoh:

```text
http://192.168.163.131:8080
```

Pada project ini service WordPress saya taruh di port 8080.

Untuk membuka dashboard administrator:

```text
http://IP-UBUNTU:8080/wp-admin
```

Contoh:

```text
http://192.168.163.131:8080/wp-admin
```

Ganti `IP-UBUNTU` dengan alamat IP Ubuntu yang digunakan.

## 6. Hasil Pengujian

Pengujian dilakukan untuk memastikan seluruh service berjalan dan aplikasi dapat digunakan.

### 6.1 Pengujian Container

Perintah:

```bash
docker compose ps
```

Hasil:

- Container WordPress berjalan.
- Container MariaDB berjalan.
- MariaDB menunjukkan status healthy.
- Port WordPress dapat diakses melalui port 8080.

### 6.2 Pengujian Browser

WordPress berhasil diakses melalui browser menggunakan alamat IP Ubuntu dan port 8080.

Halaman depan WordPress berhasil ditampilkan dan dashboard administrator dapat diakses.

### 6.3 Pengujian Database

Koneksi ke MariaDB dilakukan menggunakan:

```bash
docker exec -it wordpress-db mariadb -u root -p
```

Kemudian dilakukan pemeriksaan database:

```sql
SHOW DATABASES;
```

Database `wordpress_db` berhasil ditemukan.

Pemeriksaan tabel:

```sql
USE wordpress_db;
SHOW TABLES;
```

Tabel-tabel WordPress berhasil ditampilkan.

### 6.4 Pengujian Persistensi Data

Pengujian dilakukan dengan membuat artikel WordPress berjudul `Uji Docker Compose`.

Setelah artikel berhasil dipublikasikan, container di-restart menggunakan:

```bash
docker compose restart
```

Kemudian dilakukan pemeriksaan kembali melalui dashboard WordPress dan database MariaDB.

Hasil:

- Artikel tetap tersedia di WordPress.
- Data artikel tetap tersimpan di MariaDB.
- Data tidak hilang setelah container di-restart.

Dengan demikian, penggunaan Docker volumes berhasil mempertahankan data aplikasi.

## 7. Jawaban Pertanyaan Eksplorasi

### 1. Jelaskan tujuan Docker Compose dan alasan WordPress membutuhkan layanan database terpisah!

Docker Compose digunakan untuk mengelola beberapa container melalui satu file konfigurasi sehingga proses menjalankan aplikasi menjadi lebih mudah dan terstruktur.

WordPress membutuhkan database terpisah karena data seperti artikel, halaman, akun pengguna, komentar, dan pengaturan website disimpan dalam database.

Dengan memisahkan WordPress dan database ke dalam service berbeda, pengelolaan aplikasi menjadi lebih fleksibel dan setiap service dapat diperbarui atau dikelola secara terpisah.

### 2. Jelaskan fungsi service db dan service wordpress pada compose.yaml.

**Service db:**

Berfungsi menjalankan MariaDB sebagai database WordPress. Service ini menyimpan data aplikasi, mengatur pengguna database, serta menyediakan healthcheck untuk memeriksa kesiapan database.

**Service wordpress:**

Berfungsi menjalankan aplikasi WordPress. Service ini menyediakan antarmuka website, menerima koneksi pengguna melalui browser, dan berkomunikasi dengan database MariaDB untuk mengambil maupun menyimpan data.

### 3. Jelaskan arti image, container_name, restart, ports, environment, depends_on, dan volumes.

- **image:** Menentukan image yang digunakan untuk membuat container, misalnya `wordpress:latest` atau `mariadb:11`.
- **container_name:** Menentukan nama container.
- **restart:** Mengatur kebijakan restart container. `unless-stopped` membuat container dijalankan kembali secara otomatis kecuali dihentikan secara manual.
- **ports:** Menghubungkan port host dengan port container. Contohnya `8080:80` berarti port 8080 pada host diarahkan ke port 80 dalam container.
- **environment:** Menyediakan variabel lingkungan yang diperlukan oleh aplikasi, seperti nama database, username, dan password.
- **depends_on:** Mengatur ketergantungan antar-service. Dalam proyek ini WordPress menunggu MariaDB berstatus healthy.
- **volumes:** Menyimpan data agar tidak hilang ketika container dihapus atau dibuat ulang.

### 4. Jelaskan alasan WORDPRESS_DB_HOST menggunakan db:3306, bukan localhost:3306.

WordPress dan MariaDB berjalan di dalam container yang berbeda.

Docker Compose menyediakan jaringan internal sehingga setiap service dapat berkomunikasi menggunakan nama service sebagai hostname.

Karena service database bernama `db`, WordPress menggunakan `db:3306` untuk mengakses MariaDB.

Jika menggunakan `localhost:3306`, WordPress akan mencoba mengakses database di dalam container WordPress itu sendiri, bukan container MariaDB.

### 5. Jelaskan perbedaan fungsi .env, .env.example, dan .gitignore.

- **.env:** Menyimpan nilai konfigurasi yang digunakan secara lokal, termasuk password database.
- **.env.example:** Menyediakan contoh nama variabel dan format konfigurasi agar pengguna lain dapat membuat file `.env` sendiri.
- **.gitignore:** Menentukan file atau folder yang tidak perlu dimasukkan ke dalam Git, misalnya `.env`, agar informasi sensitif tidak terunggah ke GitHub.

Ketiganya membantu menjaga keamanan konfigurasi sekaligus mempermudah penggunaan proyek oleh orang lain.

### 6. Jelaskan apa yang dibuktikan oleh percobaan docker compose down lalu docker compose up -d.

Perintah:

```bash
docker compose down
```

Digunakan untuk menghentikan dan menghapus container serta jaringan yang dibuat oleh Docker Compose. Named volumes tidak dihapus secara default.

Kemudian:

```bash
docker compose up -d
```

Digunakan untuk membuat dan menjalankan kembali container berdasarkan konfigurasi Compose.

Jika data tetap tersedia setelah kedua perintah tersebut dijalankan, hal tersebut membuktikan bahwa data tersimpan di Docker volumes, bukan hanya di dalam filesystem container.

Catatan: Pengujian persistensi yang telah dilakukan dalam proyek ini adalah menggunakan `docker compose restart`, bukan `docker compose down` lalu `up -d`. Keduanya berbeda, sehingga pengujian `down` dan `up -d` perlu dilakukan tersendiri jika ingin mengklaim hasil pengujian tersebut.

### 7. Pilih satu masalah yang Anda temui selama setup. Jelaskan penyebab, langkah perbaikan, dan hasilnya.

Masalah yang ditemukan selama setup adalah WordPress tidak dapat dijalankan setelah konfigurasi `depends_on` diubah menjadi `condition: service_healthy`.

Penyebabnya adalah service MariaDB belum memiliki konfigurasi healthcheck, sehingga Docker Compose tidak dapat memastikan apakah database sudah siap menerima koneksi.

Langkah perbaikan:

1. Memeriksa konfigurasi `compose.yaml`.
2. Menambahkan healthcheck pada service MariaDB.
3. Menggunakan perintah `healthcheck.sh` untuk memeriksa kesiapan database.
4. Memastikan service WordPress menggunakan `condition: service_healthy`.
5. Memvalidasi konfigurasi menggunakan `docker compose config`.
6. Menjalankan kembali container menggunakan `docker compose up -d`.

Hasilnya, MariaDB berhasil menunjukkan status healthy dan WordPress dapat berjalan kembali serta diakses melalui browser.

### 8. Jelaskan satu modifikasi yang Anda lakukan sendiri.

Modifikasi yang dilakukan adalah menambahkan healthcheck pada service MariaDB dan mengatur ketergantungan WordPress menggunakan kondisi `service_healthy`.

Modifikasi ini bertujuan memastikan WordPress tidak langsung dijalankan sebelum database siap digunakan.

Selain itu, port WordPress dikonfigurasi menggunakan port 8080 pada host agar aplikasi dapat diakses melalui browser.

## 8. Dokumentasi Screenshot

Screenshot hasil pengerjaan disimpan di dalam folder `bukti-running/`.

Dokumentasi tersebut digunakan sebagai bukti bahwa:

- Container Docker berhasil berjalan.
- WordPress berhasil diakses melalui browser.
- Dashboard administrator WordPress berhasil dibuka.
- Pengujian aplikasi dan database telah dilakukan.

## 9. Kesimpulan

Melalui proyek ini, berhasil dilakukan implementasi WordPress dan MariaDB menggunakan Docker Compose.

Kedua service berhasil dijalankan dan dihubungkan melalui jaringan internal Docker. Healthcheck digunakan untuk memastikan kesiapan database sebelum WordPress dijalankan.

Penggunaan Docker volumes juga berhasil dibuktikan melalui pengujian restart container, ketika artikel WordPress tetap tersedia dan data di MariaDB tidak hilang.

Proyek ini memberikan pemahaman mengenai pengelolaan multi-container, konfigurasi environment variables, komunikasi antar-container, healthcheck, dan persistensi data menggunakan Docker Compose.
