# Docker Compose untuk Laravel
Ini adalah konfigurasi Docker Compose untuk menjalankan proyek Laravel Anda di lingkungan Docker. Ini akan memudahkan Anda dalam mengelola lingkungan pengembangan Anda dan menjalankan aplikasi Laravel tanpa harus menginstal semua dependensi secara lokal.

# Persyaratan
Sebelum Anda dapat menggunakan Docker Compose untuk Laravel, pastikan Anda telah menginstal Docker dan Docker Compose di sistem Anda.

- <a link="">Docker Installation Guide</a>
- <a link="">Docker Compose Installation Guide</a>

# Penggunaan
1. Clone repositori proyek Laravel Anda ke direktori yang sesuai di komputer Anda.
2. Buat file .env di dalam direktori proyek Laravel Anda dan konfigurasikan variabel lingkungan yang diperlukan. Pastikan Anda telah mengatur koneksi database sesuai dengan konfigurasi Docker Compose di bawah ini.
3. Salin konfigurasi Docker Compose di bawah ini ke file docker-compose.yml di dalam direktori proyek Anda.

<pre>
  <div class="bg-black rounded-md mb-4"><div class="flex items-center relative text-gray-200 bg-gray-800 px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>docker-compose.yaml</span>
    <div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs language-yaml"><span class="hljs-attr">version:</span> <span class="hljs-string">'3.8'</span>
<span class="hljs-attr">services:</span>
  <span class="hljs-attr">app:</span>
    <span class="hljs-attr">container_name:</span> <span class="hljs-string">c-laravel-app</span>
    <span class="hljs-attr">build:</span>
      <span class="hljs-attr">args:</span>
          <span class="hljs-attr">user:</span> <span class="hljs-string">rokujima</span>
          <span class="hljs-attr">uid:</span> <span class="hljs-number">1000</span>
      <span class="hljs-attr">context:</span> <span class="hljs-string">./app</span>
      <span class="hljs-attr">dockerfile:</span> <span class="hljs-string">./Dockerfile</span>
    <span class="hljs-attr">restart:</span> <span class="hljs-string">unless-stopped</span>
    <span class="hljs-attr">tty:</span> <span class="hljs-literal">true</span>
    <span class="hljs-attr">working_dir:</span> <span class="hljs-string">/var/www</span>
    <span class="hljs-attr">env_file:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./volumes/apps/.env</span>
    <span class="hljs-attr">volumes:</span> 
      <span class="hljs-bullet">-</span> <span class="hljs-string">./app:/var/www</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./volumes/apps/.env:/var/www/.env</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./volumes/php/local.ini:/usr/local/etc/php/conf.d/local.ini</span>
    <span class="hljs-attr">depends_on:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">mysql</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">redis</span>
    <span class="hljs-attr">networks:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">internal_net</span>
    
  <span class="hljs-attr">nginx:</span>
    <span class="hljs-attr">image:</span> <span class="hljs-string">nginx:alpine</span>
    <span class="hljs-attr">container_name:</span> <span class="hljs-string">c-laravel-nginx</span>
    <span class="hljs-attr">restart:</span> <span class="hljs-string">unless-stopped</span>
    <span class="hljs-attr">tty:</span> <span class="hljs-literal">true</span>
    <span class="hljs-attr">ports:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-number">80</span><span class="hljs-string">:80</span>
    <span class="hljs-attr">volumes:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./app:/var/www</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./volumes/nginx:/etc/nginx/conf.d/</span>
    <span class="hljs-attr">networks:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">internal_net</span>
    <span class="hljs-attr">depends_on:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">app</span>

  <span class="hljs-attr">redis:</span>
    <span class="hljs-attr">image:</span> <span class="hljs-string">redis:latest</span>
    <span class="hljs-attr">container_name:</span> <span class="hljs-string">c-laravel-redis</span>
    <span class="hljs-attr">restart:</span> <span class="hljs-string">always</span>
    <span class="hljs-attr">ports:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-number">6379</span><span class="hljs-string">:6379</span>
    <span class="hljs-attr">volumes:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./volumes/redis/data:/data</span>
    <span class="hljs-attr">networks:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">internal_net</span>

  <span class="hljs-attr">mysql:</span>
    <span class="hljs-attr">container_name:</span> <span class="hljs-string">c-laravel-mysql</span>
    <span class="hljs-attr">image:</span> <span class="hljs-string">mysql:latest</span>
    <span class="hljs-attr">ports:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">'3306:3306'</span> <span class="hljs-comment"># Expose the database on port 3306 (adjust if needed)</span>
    <span class="hljs-attr">command:</span> <span class="hljs-string">--default-authentication-plugin=mysql_native_password</span>
    <span class="hljs-attr">restart:</span> <span class="hljs-string">unless-stopped</span>
    <span class="hljs-attr">volumes:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./volumes/mysql/data:/data</span>
    <span class="hljs-attr">env_file:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">./volumes/mysql/.env</span>
    <span class="hljs-attr">networks:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-string">internal_net</span>

<span class="hljs-attr">networks:</span>
  <span class="hljs-attr">internal_net:</span>
    <span class="hljs-attr">driver:</span> <span class="hljs-string">bridge</span>
</code></div></div></pre>

Buat file Dockerfile di dalam direktori proyek Anda dengan konten berikut:
<pre><div class="bg-black rounded-md mb-4"><div class="flex items-center relative text-gray-200 bg-gray-800 px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>Dockerfile</span></div><div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs language-Dockerfile">FROM php:7-fpm

# Arguments defined in docker-compose.yml
ARG user
ARG uid

# Install system dependencies
RUN apt-get update &amp;&amp; apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip

# Clear cache
RUN apt-get clean &amp;&amp; rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u $uid -d /home/$user $user
RUN mkdir -p /home/$user/.composer &amp;&amp; \
    chown -R $user:$user /home/$user

# Set working directory
WORKDIR /var/www

USER $user
</code></div></div></pre>

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
# Catatan
Pastikan Anda telah mengatur variabel lingkungan yang sesuai dalam file .env proyek Laravel Anda untuk menghubungkan ke database, jika diperlukan.

Anda dapat menyesuaikan port yang digunakan dalam konfigurasi Docker Compose jika diperlukan.

Jika Anda ingin mengakses kontainer secara interaktif, gunakan perintah docker exec -it NAMA_KONTAINER /bin/bash, di mana NAMA_KONTAINER adalah nama kontainer yang sesuai.

Selamat mengembangkan proyek Laravel Anda dalam lingkungan Docker!

Dengan konfigurasi ini, Anda dapat dengan mudah mengelola dan menjalankan proyek Laravel Anda menggunakan Docker. Pastikan untuk mengganti nama kontainer, konfigurasi database, dan variabel lingkungan sesuai dengan proyek Anda.
