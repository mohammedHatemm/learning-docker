# 🚀 Laravel 12 مع Docker - دليل شامل

<div align="center">
  <img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"/>
  <br><br>

[![PHP](https://img.shields.io/badge/PHP-8.2-777BB4?style=for-the-badge&logo=php&logoColor=white)](https://php.net)
[![Laravel](https://img.shields.io/badge/Laravel-12.0-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://docker.com)
[![MariaDB](https://img.shields.io/badge/MariaDB-10.9-003545?style=for-the-badge&logo=mariadb&logoColor=white)](https://mariadb.org)

**بيئة تطوير متكاملة لـ Laravel باستخدام Docker**

</div>

---

## 📋 المحتويات

- [🎯 نظرة عامة](#-نظرة-عامة)
- [🏗️ شرح الـ Dockerfile](#️-شرح-الـ-dockerfile)
- [⚡ التشغيل السريع](#-التشغيل-السريع)
- [🐳 طرق التشغيل](#-طرق-التشغيل)
- [⚙️ أوامر Laravel](#️-أوامر-laravel)
- [🔧 الإعدادات](#-الإعدادات)
- [🌐 إعداد Nginx](#-إعداد-nginx)
- [💡 نصائح مهمة](#-نصائح-مهمة)
- [🔍 استكشاف الأخطاء](#-استكشاف-الأخطاء)
- [📝 أوامر إضافية](#-أوامر-إضافية)

---

## 🎯 نظرة عامة

> **هذا الـ setup يوفر لك بيئة تطوير كاملة ومتطورة لـ Laravel 12 باستخدام Docker**

### ✨ المميزات الرئيسية:

- 🏃‍♂️ **سرعة في الإعداد**: تشغيل Laravel في دقائق قليلة
- 🔒 **بيئة معزولة**: كل شيء منفصل عن النظام الأساسي
- 📦 **شامل**: يحتوي على كل ما تحتاجه للتطوير
- 🔄 **قابل للنقل**: يعمل على أي نظام تشغيل
- 🛡️ **آمن**: صلاحيات محددة ومضبوطة

---

## 🏗️ شرح الـ Dockerfile

<details>
<summary><strong>📖 اضغط لرؤية الشرح التفصيلي</strong></summary>

### 🐘 Base Image

```dockerfile
FROM php:8.2-fpm
```

> **لماذا PHP-FPM؟**
>
> - أداء ممتاز للـ web applications
> - إدارة أفضل للذاكرة
> - قابلية توسع عالية

### 📦 الباكجات الأساسية

```dockerfile
RUN apt-get update && apt-get install -y \
    unzip \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    mariadb-client
```

| الباكج           | الغرض منه                  |
| ---------------- | -------------------------- |
| `unzip` & `zip`  | 📁 فك وضغط الملفات         |
| `git`            | 🔄 إدارة الكود المصدري     |
| `curl`           | 🌐 طلبات HTTP              |
| `libpng-dev`     | 🖼️ معالجة الصور            |
| `mariadb-client` | 🗄️ الاتصال بقاعدة البيانات |

### 🔧 PHP Extensions

```dockerfile
docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd
```

| Extension   | الوظيفة                      |
| ----------- | ---------------------------- |
| `pdo_mysql` | 🔗 الاتصال بـ MySQL/MariaDB  |
| `mbstring`  | 📝 النصوص متعددة البايت      |
| `exif`      | 📷 معلومات الصور             |
| `gd`        | 🎨 معالجة الصور              |
| `bcmath`    | 🧮 العمليات الحسابية الدقيقة |

</details>

---

## ⚡ التشغيل السريع

### 🚀 في 3 خطوات فقط:

```bash
# 1️⃣ بناء الصورة
docker build -t laravel-app .

# 2️⃣ تشغيل الـ container
docker run -d --name laravel-container -p 9000:9000 laravel-app

# 3️⃣ إعداد Laravel
docker exec -it laravel-container php artisan key:generate
```

**🎉 تهانينا! Laravel جاهز للعمل**

---

## 🐳 طرق التشغيل

<details>
<summary><strong>🔧 التشغيل العادي</strong></summary>

```bash
# بناء الصورة
docker build -t laravel-app .

# تشغيل مع port mapping
docker run -d --name laravel-container \
  -p 9000:9000 \
  -p 8000:8000 \
  laravel-app

# تشغيل مع volume للتطوير
docker run -d --name laravel-container \
  -p 9000:9000 \
  -v $(pwd)/app:/var/www/app \
  laravel-app
```

</details>

<details>
<summary><strong>⚙️ Docker Compose (مُفضل)</strong></summary>

### 📄 إنشاء `docker-compose.yml`

```yaml
version: "3.8"

services:
  # 🐘 Laravel Application
  app:
    build: .
    container_name: laravel-app
    restart: unless-stopped
    working_dir: /var/www/app
    volumes:
      - ./app:/var/www/app
    ports:
      - "9000:9000"
    networks:
      - laravel-network
    depends_on:
      - db
    environment:
      - DB_HOST=db
      - DB_DATABASE=laravel
      - DB_USERNAME=laravel
      - DB_PASSWORD=secret

  # 🌐 Nginx Web Server
  nginx:
    image: nginx:alpine
    container_name: laravel-nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./app:/var/www/app
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app
    networks:
      - laravel-network

  # 🗄️ MariaDB Database
  db:
    image: mariadb:10.9
    container_name: laravel-db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: secret
    volumes:
      - db_data:/var/lib/mysql
      - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "3306:3306"
    networks:
      - laravel-network

  # 📮 Redis Cache
  redis:
    image: redis:alpine
    container_name: laravel-redis
    restart: unless-stopped
    ports:
      - "6379:6379"
    networks:
      - laravel-network

volumes:
  db_data:
    driver: local

networks:
  laravel-network:
    driver: bridge
```

### 🎬 تشغيل Docker Compose

```bash
# تشغيل في الخلفية
docker-compose up -d

# مشاهدة الـ logs
docker-compose logs -f

# إيقاف الخدمات
docker-compose down

# إعادة بناء وتشغيل
docker-compose up --build -d
```

</details>

---

## ⚙️ أوامر Laravel

### 🖥️ الدخول للـ Container

```bash
# طريقة تفاعلية
docker exec -it laravel-container bash

# تشغيل bash مع المستخدم المناسب
docker exec -it --user www-data laravel-container bash
```

### 🎨 أوامر Artisan

<div align="center">

| الأمر | الوصف               | المثال                                       |
| ----- | ------------------- | -------------------------------------------- |
| 🔑    | إنشاء مفتاح التطبيق | `php artisan key:generate`                   |
| 🗄️    | تشغيل migrations    | `php artisan migrate`                        |
| 🏭    | إنشاء controller    | `php artisan make:controller UserController` |
| 📦    | إنشاء model         | `php artisan make:model User -m`             |
| 🌱    | تشغيل seeders       | `php artisan db:seed`                        |
| 🧹    | مسح cache           | `php artisan cache:clear`                    |

</div>

```bash
# 🏃‍♂️ من خارج الـ container
docker exec -it laravel-container php artisan migrate
docker exec -it laravel-container php artisan make:controller ApiController --api

# 💻 من داخل الـ container
docker exec -it laravel-container bash
php artisan tinker
php artisan serve --host=0.0.0.0 --port=8000
```

### 📦 أوامر Composer

```bash
# تثبيت packages
docker exec -it laravel-container composer install
docker exec -it laravel-container composer require spatie/laravel-permission

# تحديث packages
docker exec -it laravel-container composer update

# إضافة package للتطوير فقط
docker exec -it laravel-container composer require --dev barryvdh/laravel-debugbar
```

---

## 🔧 الإعدادات

### 🌍 ملف البيئة `.env`

```bash
# نسخ ملف البيئة
docker exec -it laravel-container cp .env.example .env

# إنشاء مفتاح التطبيق
docker exec -it laravel-container php artisan key:generate

# تحرير الملف
docker exec -it laravel-container nano .env
```

### 📝 إعدادات قاعدة البيانات

```env
# 🗄️ Database Configuration
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret

# 📮 Redis Configuration
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379

# 📧 Mail Configuration
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
```

### 🏃‍♂️ أول إعداد

```bash
# 1. إعداد البيئة
docker exec -it laravel-container cp .env.example .env
docker exec -it laravel-container php artisan key:generate

# 2. تشغيل migrations
docker exec -it laravel-container php artisan migrate

# 3. إنشاء symbolic link للتخزين
docker exec -it laravel-container php artisan storage:link

# 4. مسح cache
docker exec -it laravel-container php artisan config:cache
docker exec -it laravel-container php artisan route:cache
docker exec -it laravel-container php artisan view:cache
```

---

## 🌐 إعداد Nginx

### 📁 إنشاء مجلد nginx

```bash
mkdir -p nginx
```

### ⚙️ ملف `nginx/nginx.conf`

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name localhost;
    root /var/www/app/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    # 📁 Static files caching
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|pdf)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass app:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_hide_header X-Powered-By;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

---

## 💡 نصائح مهمة

### 🔥 للتطوير

<details>
<summary><strong>🛠️ نصائح التطوير</strong></summary>

```bash
# 🔄 Live reload مع volumes
docker run -d --name laravel-dev \
  -p 80:80 -p 9000:9000 \
  -v $(pwd)/app:/var/www/app \
  -v $(pwd)/nginx:/etc/nginx/conf.d \
  laravel-app

# 📊 مراقبة الأداء
docker stats laravel-container

# 🔍 مشاهدة logs في الوقت الفعلي
docker logs -f laravel-container
docker exec -it laravel-container tail -f storage/logs/laravel.log
```

</details>

### 🚀 للإنتاج

<details>
<summary><strong>⚡ تحسينات الإنتاج</strong></summary>

```bash
# 💨 تحسين Laravel للإنتاج
docker exec -it laravel-container php artisan config:cache
docker exec -it laravel-container php artisan route:cache
docker exec -it laravel-container php artisan view:cache
docker exec -it laravel-container php artisan event:cache

# 🗜️ تحسين Composer
docker exec -it laravel-container composer install --optimize-autoloader --no-dev

# 🧹 تنظيف cache التطوير
docker exec -it laravel-container php artisan cache:clear
docker exec -it laravel-container php artisan view:clear
```

</details>

### 💾 النسخ الاحتياطية

```bash
# 🗄️ نسخة من قاعدة البيانات
docker exec laravel-db mysqldump -u laravel -psecret laravel > backup_$(date +%Y%m%d_%H%M%S).sql

# 📁 نسخة من الملفات المرفوعة
docker cp laravel-container:/var/www/app/storage/app/public ./backup/storage

# ♻️ استعادة قاعدة البيانات
docker exec -i laravel-db mysql -u laravel -psecret laravel < backup.sql
```

---

## 🔍 استكشاف الأخطاء

### ⚠️ المشاكل الشائعة

<details>
<summary><strong>🔐 مشاكل الصلاحيات</strong></summary>

```bash
# إصلاح صلاحيات Laravel
docker exec -it laravel-container chown -R www-data:www-data /var/www/app
docker exec -it laravel-container chmod -R 755 /var/www/app/storage
docker exec -it laravel-container chmod -R 755 /var/www/app/bootstrap/cache

# صلاحيات خاصة للملفات الحساسة
docker exec -it laravel-container chmod 644 /var/www/app/.env
```

</details>

<details>
<summary><strong>🗄️ مشاكل قاعدة البيانات</strong></summary>

```bash
# فحص حالة الخدمات
docker-compose ps

# اختبار الاتصال بقاعدة البيانات
docker exec -it laravel-container php artisan tinker
# في tinker: DB::connection()->getPdo();

# فحص logs قاعدة البيانات
docker logs laravel-db

# إعادة تشغيل قاعدة البيانات
docker-compose restart db
```

</details>

<details>
<summary><strong>🌐 مشاكل الشبكة والبورتات</strong></summary>

```bash
# فحص البورتات المستخدمة
netstat -tlnp | grep :80
netstat -tlnp | grep :3306
netstat -tlnp | grep :9000

# فحص شبكة Docker
docker network ls
docker network inspect laravel_laravel-network

# إعادة إنشاء الشبكة
docker-compose down
docker network prune
docker-compose up -d
```

</details>

---

## 📝 أوامر إضافية

### 🔧 إدارة Containers

```bash
# عرض جميع الـ containers
docker ps -a

# حذف container
docker rm -f laravel-container

# حذف جميع containers المتوقفة
docker container prune

# مراقبة استخدام الموارد
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}"
```

### 🖼️ إدارة Images

```bash
# عرض جميع الصور
docker images

# حذف صورة معينة
docker rmi laravel-app

# حذف الصور غير المستخدمة
docker image prune -a

# بناء بدون cache
docker build --no-cache -t laravel-app .
```

### 🧹 تنظيف النظام

```bash
# تنظيف شامل
docker system prune -a --volumes

# تنظيف volumes
docker volume prune

# تنظيف networks
docker network prune

# عرض استخدام المساحة
docker system df
```

---

## 🎯 الخلاصة

<div align="center">

### ✅ ما حصلت عليه:

🏗️ **بيئة تطوير متكاملة** لـ Laravel 12
🐳 **Docker setup محسّن** للأداء والأمان
🔧 **إعدادات جاهزة** للتطوير والإنتاج
📚 **دليل شامل** لجميع الأوامر والإعدادات

### 🚀 الخطوات التالية:

1. **ابدأ التطوير**: أنشئ controllers و models جديدة
2. **أضف packages**: استخدم Composer لتثبيت المكتبات
3. **صمم قاعدة البيانات**: أنشئ migrations و seeders
4. **طوّر APIs**: استخدم Laravel Resources و Controllers

</div>

---

<div align="center">

**🎉 مبروك! أصبحت جاهزاً للتطوير مع Laravel و Docker**

[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com)
[![Docker Hub](https://img.shields.io/badge/Docker%20Hub-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://hub.docker.com)
[![Laravel Docs](https://img.shields.io/badge/Laravel%20Docs-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com/docs)

</div>
