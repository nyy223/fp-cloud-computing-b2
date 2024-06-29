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
Kami memutuskan untuk menggunakan Digital Ocean sebagai cloud provider untuk final project kami.

#### Rancangan Arsitektur
![final project](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/2783e2ea-3ca5-4ed5-b507-27d8f761b3d1)

#### Revisi rancangan arsitektur
![final project](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/755f0a3e-852d-41f6-aeec-7bd3169c2290)

#### Tabel Harga
| No. | Nama              | Spesifikasi                                                                 | Fungsi                              | Harga/Bulan |
|-----|-------------------|-----------------------------------------------------------------------------|-------------------------------------|-------------|
| 1.  | worker_1 | 2 Intel vCPU / 2GB Memory / 90GB Disk / SGP1 - Ubuntu 24.04 (LTS) x64   | App Worker 1                        | 24$         |
| 2.  | worker_2 | 1 Intel vCPU / 2GB Memory / 70GB Disk / SGP1 - Ubuntu 24.04 (LTS) x64   | App Worker 2                        | 16$         |
| 3.  | mongo_db | 1 Intel vCPU / 1GB Memory / 15GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64   | Mongo Database                      | 15$         |
| 4.  | load_balancing | 1 Intel vCPU / 512MB Memory / 10GB Disk SSD / SGP1 - Ubuntu 24.04 (LTS) x64 | Load Balancer       | 4$          |
|     |                   |                                                                             | **Total**                           | **59$**     |

## Konfigurasi Worker 1 & Worker 2
1. Buat database dan copy connection string dengan menggunakan aplikasi MongoDB Compass
![WhatsApp Image 2024-06-25 at 23 21 32](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/e979326f-7be2-46dc-984a-bfdb26e8e585)

2. Menyambungkan terminal laptop ke terminal VM
```bash
ssh root@ip-worker
```
#### worker 1
<img width="667" alt="Screenshot 2024-06-28 at 23 46 51" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/2fee123c-d53a-4c44-8b72-f0196135a855">

#### worker 2
<img width="671" alt="Screenshot 2024-06-28 at 23 47 17" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/702106b3-71c6-472d-b5f5-8b2506ba3c1b">

3. Install resource yang dibutuhkan
```bash
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/index.html
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/styles.css
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/BE/sentiment-analysis.py
```

4. Install nginx
```bash
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

5. Install dependency python
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

6. Ubah fetch pada index.html agar mengarah ke ip worker yang dituju
#### worker 1
<img width="850" alt="Screenshot 2024-06-28 at 23 50 29" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/fb76af11-4604-4a4d-a16e-ef30603db828">

#### worker 2
<img width="872" alt="Screenshot 2024-06-28 at 23 48 36" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/2a87c0e4-7785-4680-a839-3a6f878b5e5e">


7. Pindahkan resources untuk FE ke /var/www/html
```bash
mv index.html /var/www/html/index.html && mv styles.css /var/www/html/styles.css
```

8. Konfigurasi ip database pada sentiment-analysis.py
<img width="692" alt="Screenshot 2024-06-21 at 17 41 28" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/e6f0e355-95bd-4a7d-a8ac-9744e660ddc4">

9. Restart nginx
```bash
sudo service nginx restart
```

10. Jalankan sentiment-analysis.py
(pastikan virtual environment sudah aktif)
```bash
gunicorn --bind 0.0.0.0:5000 -w 8 -k gevent --timeout 120 sentiment-analysis:app
```
<img width="984" alt="image" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/a6768b79-daa8-4d49-b826-ef29fbebeb2f">

## Konfigurasi VM Load Balancer
1. Menyambungkan load balancer ke terminal
```bash
ssh root@ip-lb
```
<img width="605" alt="Screenshot 2024-06-29 at 00 30 15" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/87514c72-395f-4f75-9614-544b8ce3a8ca">
 
2. Instalasi Nginx
```bash
sudo apt update
sudo apt install nginx
```
3. Modifikasi /etc/nginx/nginx.conf
```bash
user www-data;
worker_processes auto;
pid /run/nginx.pid;
worker_rlimit_nofile 100000;


events {
    worker_connections 4096;
    multi_accept on;
    use epoll;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;

    keepalive_timeout 65;
    types_hash_max_size 2048;

    server_tokens off;

    # Cache configuration
    proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=1g inactive=60m use_temp_path=off;
    proxy_temp_path /var/cache/nginx/temp;
    proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
    proxy_cache_lock on;
    proxy_cache_lock_timeout 5s;

    gzip on;
    gzip_comp_level 2;
    gzip_proxied any;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```
4. Buat konfigurasi untuk Load balancer pada /etc/nginx/sites-available/load_balancer
```bash
upstream backend {
    server 159.223.67.51:5000;
    server 167.172.80.197:5000;
}

server {
    listen 80;
    server_name 152.42.248.218;  # Ganti dengan IP load balancer

    location / {
        proxy_cache my_cache;
        proxy_cache_valid 200 302 10m;
        proxy_cache_valid 404 1m;

        proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
        proxy_cache_lock on;
        proxy_cache_lock_timeout 5s;

        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        add_header X-Cached $upstream_cache_status;

        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        send_timeout 60s;
    }
}
```
5. Aktifkan konfigurasi load balancer
```bash
sudo ln -s /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
```
6. Uji Konfigurasi dan Reload Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```

## Hasil Pengujian Setiap Endpoint
![WhatsApp Image 2024-06-28 at 23 12 18](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/143aa57a-60fc-44df-afe0-a7a9a01d17f6)
![WhatsApp Image 2024-06-28 at 23 10 46](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/7dfe295e-c29f-4d61-a906-ec6df76624b5)

### Pengujian Frontend (salah satu worker)
![WhatsApp Image 2024-06-28 at 16 35 03](https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/2c031764-cf01-452f-91e9-2f44236b19fa)

## Hasil Pengujian dan Analisis Loadtesting Locust
- Peak Concurrency Maksimum 1000 (spawn rate 50, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/2df444a6-7430-4829-abca-547a8387efc9)
Dari hasil tersebut didapatkan Rata rata RPS dengan failure 0% adalah 328.7 RPS, maka nilainya 328.7 / 200 x 30 = 49.3 poin
- Peak Concurrency Maksimum 2000 (spawn rate 100, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/395c5414-7048-4cc0-93a4-f99608ba16d0)
Dari hasil tersebut didapatkan Rata rata RPS dengan failure 0% adalah 584.4 RPS, maka nilainya 584.4 / 200 x 30 = 87.6 poin
- Peak Concurrency Maksimum 3000 (spawn rate 200, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/33448e20-1112-40ff-9efc-46e6e135a9f5)
Dari hasil tersebut didapatkan Rata rata RPS dengan failure 0% adalah 581.1 RPS, maka nilainya 581.1 / 200 x 30 = 87.16 poin
- Peak Concurrency Maksimum 4000 (spawn rate 500, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/fac5b3f5-8160-4756-837b-88f7f52fdf6c)
Dari hasil tersebut didapatkan Rata rata RPS dengan failure 0% adalah 617.4 RPS, maka nilainya 617.4 / 200 x 30 = 92.6 poin

## Revisi, Kesimpulan dan Saran
### Revisi
1. Mengubah Rancangan Arsitektur: Pada awalnya, kami menggunakan dua node load balancer yang disediakan oleh Digital Ocean. Namun, tingkat kegagalan masih tinggi dan jumlah requests per second (RPS) yang diperoleh belum memadai. Setelah kami mencoba mengonfigurasi load balancer secara mandiri tanpa menggunakan layanan yang disediakan oleh Digital Ocean, RPS yang diperoleh meningkat secara signifikan dibandingkan sebelumnya.
2. Meningkatkan Spesifikasi Worker untuk VM: Mengganti worker dengan VM yang memiliki spesifikasi lebih tinggi dan lebih baik juga dapat meningkatkan jumlah requests per second (RPS). CPU dengan lebih banyak core memungkinkan pemrosesan lebih banyak permintaan dalam waktu yang lebih singkat, sehingga dapat meningkatkan RPS. Selain itu, kapasitas RAM yang lebih besar memungkinkan aplikasi untuk menyimpan lebih banyak data dalam memori, mengurangi waktu yang dihabiskan untuk pengambilan data dari disk yang lebih lambat, serta memungkinkan penanganan lebih banyak permintaan secara bersamaan.

### Kesimpulan
Melakukan konfigurasi load balancer secara mandiri dapat menghasilkan kinerja yang lebih baik dibandingkan dengan menggunakan load balancer yang disediakan oleh penyedia layanan cloud seperti Digital Ocean. Hal ini terbukti dari peningkatan signifikan dalam jumlah requests per second (RPS) setelah melakukan perubahan tersebut. Selain itu, mengganti virtual machine (VM) yang memiliki spesifikasi lebih tinggi dapat menghasilkan hasil akhir yang lebih optimal. 

### Saran
1. Pertimbangan Spesifikasi VM: Spesifikasi VM seperti jumlah core CPU dan kapasitas RAM sangat penting dalam menentukan kinerja aplikasi. Memilih VM dengan spesifikasi yang lebih tinggi dapat meningkatkan kemampuan pemrosesan permintaan dan efisiensi penyimpanan data dalam memori, sehingga mengoptimalkan RPS.
2. Optimasi Infrastruktur: Pengaturan dan optimasi infrastruktur, termasuk load balancer dan VM, dapat berdampak besar pada kinerja sistem. Menginvestasikan waktu dan sumber daya untuk menyesuaikan konfigurasi infrastruktur sesuai kebutuhan dapat memberikan hasil yang lebih baik daripada menggunakan solusi standar.
* Monitoring dan Analisis Kinerja: Penting untuk terus memonitor dan menganalisis kinerja sistem untuk mengidentifikasi bottleneck dan area yang dapat dioptimalkan. Ini membantu dalam membuat keputusan yang berdasarkan data untuk meningkatkan kinerja aplikasi.
* Load Balancing yang Efektif: Menggunakan load balancer yang terkonfigurasi dengan baik dapat mendistribusikan beban secara merata di seluruh server. Hal ini tidak hanya meningkatkan kinerja tetapi juga memastikan kelancaran jaringan dan mencegah satu titik kegagalan.
* Kelancaran koneksi internet: Stabilitas koneksi internet yang baik memastikan bahwa permintaan dari klien dapat diterima dan diteruskan ke server tanpa gangguan. Koneksi yang tidak stabil dapat menyebabkan kehilangan paket data atau latency tinggi, yang mengurangi efisiensi load balancer. Selain itu, load balancer sering kali menangani banyak permintaan sekaligus, sehingga memerlukan bandwidth yang cukup untuk menjalankan operasinya secara efektif. Koneksi internet yang buruk dengan bandwidth terbatas dapat menjadi bottleneck, mengurangi kemampuan load balancer untuk menangani beban kerja tinggi.

## Video revisi
https://youtu.be/VskXtc3cLOE?si=KfP6XimP0mqQ1vdI 
