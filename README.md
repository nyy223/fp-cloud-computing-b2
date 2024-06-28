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

## Konfigurasi Worker 1 & Worker 2
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

5. Ubah fetch pada index.html agar mengarah ke ip worker yang dituju
<img width="880" alt="Screenshot 2024-06-21 at 17 29 38" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/7eb22fbe-4fef-461c-908b-161aeb2f0769">
<img width="878" alt="Screenshot 2024-06-21 at 17 29 57" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/0dfff9f2-1119-4419-a07d-bbbef6a41bbd">

6. Pindahkan resources untuk FE ke /var/www/html
```bash
mv index.html /var/www/html/index.html && mv styles.css /var/www/html/styles.css
```

7. Konfigurasi ip database pada sentiment-analysis.py
<img width="692" alt="Screenshot 2024-06-21 at 17 41 28" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/e6f0e355-95bd-4a7d-a8ac-9744e660ddc4">

8. Restart nginx
```bash
sudo service nginx restart
```

9. Jalankan sentiment-analysis.py
(pastikan virtual environment sudah aktif)
```bash
gunicorn --bind 0.0.0.0:5000 -w 8 -k gevent --timeout 120 sentiment-analysis:app
```
<img width="984" alt="image" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/a6768b79-daa8-4d49-b826-ef29fbebeb2f">

## Konfigurasi VM Load Balancer
1. Instalasi Nginx
```bash
sudo apt update
sudo apt install nginx
```
2. Modifikasi /etc/nginx/nginx.conf
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
3. Buat konfigurasi untuk Load balancer
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
4. Aktifkan konfigurasi load balancer
```bash
sudo ln -s /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
```
5. Uji Konfigurasi dan Reload Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```

## Hasil Pengujian Setiap Endpoint
### Postman
<img width="1404" alt="Screenshot 2024-06-21 at 20 05 04" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/26bdca23-379f-48ef-acca-34ad05139366">
<img width="1373" alt="Screenshot 2024-06-21 at 20 07 16" src="https://github.com/nyy223/fp-cloud-computing-b2/assets/80509033/3c195768-fe8c-4deb-b503-02f78acb5779">

## Hasil Pengujian dan Analisis Loadtesting Locust
- Peak Concurrency Maksimum 1000 (spawn rate 50, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/2df444a6-7430-4829-abca-547a8387efc9)
- Peak Concurrency Maksimum 2000 (spawn rate 100, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/395c5414-7048-4cc0-93a4-f99608ba16d0)
- Peak Concurrency Maksimum 3000 (spawn rate 200, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/33448e20-1112-40ff-9efc-46e6e135a9f5)
- Peak Concurrency Maksimum 4000 (spawn rate 500, load testing 60 detik)
![image](https://github.com/nyy223/fp-cloud-computing-b2/assets/164857172/fac5b3f5-8160-4756-837b-88f7f52fdf6c)



