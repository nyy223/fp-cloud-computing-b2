# Final Project

### Teknologi Komputasi Awan
#### Kelas B Kelompok 2

| Nama  | NRP  |
|----------|----------|
| Fadlillah Cantika S H  | 5027231042 |
| Nayla Raissa Azzahra  | 5027231054 |
| Aisha Ayya Ratiandari  | 5027231056 |
| Adlya Isriena Aftarisya  | 5027231066 |
| Aisyah Rahmasari | 5027231072 |

## Permasalahan
Anda adalah seorang lulusan Teknologi Informasi, sebagai ahli IT, salah satu kemampuan yang harus dimiliki adalah Keampuan merancang, membangun, mengelola aplikasi berbasis komputer menggunakan layanan awan untuk memenuhi kebutuhan organisasi.

Pada suatu saat anda mendapatkan project untuk mendeploy sebuah aplikasi Sentiment Analysis dengan komponen Backend menggunakan python: sentiment-analysis.py dengan spesifikasi sebagai berikut
<img width="693" alt="Screenshot 2024-06-21 at 07 56 58" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/556c7f25-d4cc-42d3-92fe-c8522542c758">

Kemudian juga disediakan sebuah Frontend sederhana menggunakan index.html dan styles.css dengan tampilan antarmuka sebagai berikut
<img width="1170" alt="image" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/bc9a01d4-6fea-4dec-9f46-caed98c80239">

Kemudian anda diminta untuk mendesain arsitektur cloud yang sesuai dengan kebutuhan aplikasi tersebut. Apabila dana maksimal yang diberikan adalah 1 juta rupiah per bulan (65 US$) konfigurasi cloud terbaik seperti apa yang bisa dibuat?

## Rancangan Arsitektur Awan
Kami memutuskan untuk menggunakan Digital Ocean sebagai  

#### Rancangan Arsitektur
![final project](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/2783e2ea-3ca5-4ed5-b507-27d8f761b3d1)

#### Tabel Harga
| No. | Nama              | Spesifikasi                                                                 | Fungsi                              | Harga/Bulan |
|-----|-------------------|-----------------------------------------------------------------------------|-------------------------------------|-------------|
| 1.  | worker_1 | 2 Intel vCPU / 2GB Memory / 60GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64   | App Worker 1                        | 18$         |
| 2.  | worker_2 | 2 Intel vCPU / 2GB Memory / 60GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64   | App Worker 2                        | 18$         |
| 3.  | mongo_db | 2 Intel vCPU / 2GB Memory / 60GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64   | Mongo Database                      | 18$         |
| 4.  | load_balancing | 1 Intel vCPU / 1GB Memory / 10GB Disk SSD / Asia-Southeast2(Jakarta) - Ubuntu 24.04 (LTS) x64 | Load Balancer 1 (Round-Robin)       | 6$          |
|     |                   |                                                                             | **Total**                           | **64$**     |

## Implementasi
