FROM php:8.3-apache

# 1. Herramientas del sistema
RUN apt-get update && apt-get install -y \
    git unzip curl gnupg \
    libzip-dev libpng-dev libjpeg-dev \
    && rm -rf /var/lib/apt/lists/*

# 2. Node 20 para Vite
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@latest

# 3. Extensiones PHP para Laravel
RUN docker-php-ext-install pdo pdo_mysql zip exif pcntl gd

# 4. Instalar Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 5. Copiar configuración Apache
# (ruta relativa desde docker/php)
COPY apache.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite

WORKDIR /var/www/html

# 6. Copiar el proyecto Laravel (que está en /src, dos niveles arriba)
COPY ../../src/ .

# 7. Instalar dependencias PHP
RUN composer install --no-dev --optimize-autoloader

# 8. Compilar assets Vite
RUN npm install && npm run build

# 9. Permisos Laravel
RUN chmod -R 775 storage bootstrap/cache

CMD ["apache2-foreground"]
