FROM php:8.3-apache

# 1. Herramientas del sistema
RUN apt-get update && apt-get install -y \
    git unzip curl gnupg \
    libzip-dev libpng-dev libjpeg-dev \
    && rm -rf /var/lib/apt/lists/*

# 2. Node 20 para compilar Vite
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@latest

# 3. Extensiones PHP que Laravel necesita
RUN docker-php-ext-install pdo pdo_mysql zip exif pcntl gd

# 4. Composer dentro de la imagen
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 5. Configuración de Apache
# (OJO: el apache.conf está en docker/php/apache.conf)
COPY ./apache.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite

# 6. Directorio donde copiaremos el proyecto
WORKDIR /var/www/html

# 7. Copiar la app Laravel dentro del contenedor
# (IMPORTANTE: la app está en src/*)
COPY ../../src/ .

# 8. Instalar dependencias PHP
RUN composer install --no-dev --optimize-autoloader

# 9. Compilar assets con Vite
RUN npm install && npm run build

# 10. Permisos para Laravel
RUN chmod -R 775 storage bootstrap/cache

CMD ["apache2-foreground"]
