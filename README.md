#Docker Compose untuk Laravel
Ini adalah konfigurasi Docker Compose untuk menjalankan proyek Laravel Anda di lingkungan Docker. Ini akan memudahkan Anda dalam mengelola lingkungan pengembangan Anda dan menjalankan aplikasi Laravel tanpa harus menginstal semua dependensi secara lokal.

Persyaratan
Sebelum Anda dapat menggunakan Docker Compose untuk Laravel, pastikan Anda telah menginstal Docker dan Docker Compose di sistem Anda.

Docker Installation Guide
Docker Compose Installation Guide
Penggunaan
Clone repositori proyek Laravel Anda ke direktori yang sesuai di komputer Anda.

Buat file .env di dalam direktori proyek Laravel Anda dan konfigurasikan variabel lingkungan yang diperlukan. Pastikan Anda telah mengatur koneksi database sesuai dengan konfigurasi Docker Compose di bawah ini.

Salin konfigurasi Docker Compose di bawah ini ke file docker-compose.yml di dalam direktori proyek Anda.

yaml
Copy code
version: '3.8'
services:
  app:
    container_name: c-laravel-app
    build:
      args:
          user: rokujima
          uid: 1000
      context: ./app
      dockerfile: ./Dockerfile
    restart: unless-stopped
    tty: true
    working_dir: /var/www
    env_file:
      - ./volumes/apps/.env
    volumes: 
      - ./app:/var/www
      - ./volumes/apps/.env:/var/www/.env
      - ./volumes/php/local.ini:/usr/local/etc/php/conf.d/local.ini
    depends_on:
      - mysql
      - redis
    networks:
      - internal_net
    
  nginx:
    image: nginx:alpine
    container_name: c-laravel-nginx
    restart: unless-stopped
    tty: true
    ports:
      - 80:80
    volumes:
      - ./app:/var/www
      - ./volumes/nginx:/etc/nginx/conf.d/
    networks:
      - internal_net
    depends_on:
      - app

  redis:
    image: redis:latest
    container_name: c-laravel-redis
    restart: always
    ports:
      - 6379:6379
    volumes:
      - ./volumes/redis/data:/data
    networks:
      - internal_net

  mysql:
    container_name: c-laravel-mysql
    image: mysql:latest
    ports:
      - '3306:3306' # Expose the database on port 3306 (adjust if needed)
    command: --default-authentication-plugin=mysql_native_password
    restart: unless-stopped
    volumes:
      - ./volumes/mysql/data:/data
    env_file:
      - ./volumes/mysql/.env
    networks:
      - internal_net

networks:
  internal_net:
    driver: bridge
Buat file Dockerfile di dalam direktori proyek Anda dengan konten berikut:
Dockerfile
Copy code
FROM php:7-fpm

# Arguments defined in docker-compose.yml
ARG user
ARG uid

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u $uid -d /home/$user $user
RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user

# Set working directory
WORKDIR /var/www

USER $user
Jalankan perintah berikut untuk membangun dan menjalankan kontainer Docker:
bash
Copy code
docker-compose up -d --build
Ini akan membuat dan menjalankan kontainer Docker sesuai dengan konfigurasi yang didefinisikan dalam docker-compose.yml.

Buka browser Anda dan akses aplikasi Laravel melalui http://localhost. Anda sekarang seharusnya dapat mengakses dan mengembangkan proyek Laravel Anda dalam lingkungan Docker.
Catatan
Pastikan Anda telah mengatur variabel lingkungan yang sesuai dalam file .env proyek Laravel Anda untuk menghubungkan ke database, jika diperlukan.

Anda dapat menyesuaikan port yang digunakan dalam konfigurasi Docker Compose jika diperlukan.

Jika Anda ingin mengakses kontainer secara interaktif, gunakan perintah docker exec -it NAMA_KONTAINER /bin/bash, di mana NAMA_KONTAINER adalah nama kontainer yang sesuai.

Selamat mengembangkan proyek Laravel Anda dalam lingkungan Docker!

Dengan konfigurasi ini, Anda dapat dengan mudah mengelola dan menjalankan proyek Laravel Anda menggunakan Docker. Pastikan untuk mengganti nama kontainer, konfigurasi database, dan variabel lingkungan sesuai dengan proyek Anda.
