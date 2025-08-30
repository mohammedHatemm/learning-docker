# نبدأ من صورة php مع fpm
FROM php:8.2-fpm

# نثبت الباكجات الأساسية اللي Laravel بيحتاجها
RUN apt-get update && apt-get install -y \
    unzip \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    mariadb-client \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# نثبت Composer
COPY --from=composer:2.7 /usr/bin/composer /usr/bin/composer

# نحدد مكان المشروع
WORKDIR /var/www

# ننزل Laravel جديد في مجلد اسمه app
RUN composer create-project laravel/laravel="^12.0" app

# ندخل جوا المجلد
WORKDIR /var/www/app

# ندي صلاحيات للمجلدات المهمة
RUN chown -R www-data:www-data /var/www/app \
    && chmod -R 755 /var/www/app/storage \
    && chmod -R 755 /var/www/app/bootstrap/cache

# نفتح بورت php-fpm
EXPOSE 9000

# نشغل php-fpm
CMD ["php-fpm"]
