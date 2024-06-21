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
| 1.  | worker_1 | 2 Intel vCPU / 2GB Memory / 60GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64   | App Worker 1                        | 12$         |
| 2.  | worker_2 | 2 Intel vCPU / 2GB Memory / 60GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64   | App Worker 2                        | 12$         |
| 3.  | mongo_db | 2 Intel vCPU / 2GB Memory / 60GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64   | Mongo Database                      | 15$         |
| 4.  | load_balancing | 1 Intel vCPU / 1GB Memory / 10GB Disk SSD / Asia-Southeast2(Jakarta) - Ubuntu 24.04 (LTS) x64 | Load Balancer 1 (Round-Robin)       | 24$          |
|     |                   |                                                                             | **Total**                           | **63$**     |

## Implementasi
### Konfigurasi Worker 1 & Worker 2
1. Menyambungkan terminal laptop ke terminal VM
```bash
ssh root@64.227.10.125
```
<img width="682" alt="Screenshot 2024-06-21 at 17 11 40" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/a9f7e4a6-eb1e-4ec4-b5ce-055cd4a7531b">

2. Install resource yang dibutuhkan
```bash
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/index.html
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/styles.css
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/BE/sentiment-analysis.py
```

3. Install nginx
```bash
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

4. Install dependency python
```bash
sudo apt update
sudo apt install python3 -y
sudo apt install python3-pip -y
sudo apt install python3.12-venv

python3 -m venv myenv
source myenv/bin/activate

pip install flask
pip install flask_cors
pip install gunicorn
pip install flask_pymongo
pip install textblob
pip install pymongo
pip install gevent
```
```bash
mv index.html /var/www/html/index.html
```

5. Ubah fetch pada index.html agar mengarah ke ip worker yang dituju
<img width="880" alt="Screenshot 2024-06-21 at 17 29 38" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/7eb22fbe-4fef-461c-908b-161aeb2f0769">
<img width="878" alt="Screenshot 2024-06-21 at 17 29 57" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/0dfff9f2-1119-4419-a07d-bbbef6a41bbd">

6. Konfigurasi /etc/nginx/sites-enabled/default
<img width="557" alt="Screenshot 2024-06-21 at 17 33 37" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/c0a805bc-e355-4df4-a3c5-85b2a858fae7">

7. Konfigurasi ip database pada sentiment-analysis.py
<img width="692" alt="Screenshot 2024-06-21 at 17 41 28" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/e6f0e355-95bd-4a7d-a8ac-9744e660ddc4">

8. Restart nginx
```bash
sudo service nginx restart
```

9. Jalankan sentiment-analysis.py
<img width="886" alt="Screenshot 2024-06-21 at 16 50 25" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/961c169a-a70d-4cc6-8369-1a82ab5f40c4">
<img width="527" alt="Screenshot 2024-06-21 at 17 48 10" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/dafba215-fde5-445e-843f-c4b828fadbad">

10. Buat Load Balancer dan pilih droplet worker yang sudah dibuat sebelumnya.
<img width="1170" alt="Screenshot 2024-06-21 at 18 01 10" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/f02e85cf-6c6c-4ca6-9340-dc11f9dae853">

## Pengujian
### Postman
<img width="1404" alt="Screenshot 2024-06-21 at 20 05 04" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/26bdca23-379f-48ef-acca-34ad05139366">
<img width="1373" alt="Screenshot 2024-06-21 at 20 07 16" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/3c195768-fe8c-4deb-b503-02f78acb5779">

### Locus
![WhatsApp Image 2024-06-21 at 20 13 02](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/01a52432-2cf4-4c18-abcc-c04cd6755054)
![WhatsApp Image 2024-06-21 at 20 20 09](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/e6337dc9-0c34-4a6a-ac61-35b7d1efeeb4)

