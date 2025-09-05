# ============================
# 1. Node build stage (Tailwind + JS)
# ============================
FROM node:22 AS frontend

WORKDIR /app

# Copy package.json & package-lock.json for caching
# COPY package*.json ./

# Install Node dependencies
# RUN npm install

# Copy frontend sources (JS + Tailwind CSS)
# COPY public/js ./public/js
# COPY public/css ./public/css
# COPY tailwind.config.js ./

# Build Tailwind CSS (produces public/css/output.css)
# RUN npx tailwindcss -i ./public/css/input.css -o ./public/css/output.css --minify

# ============================
# 2. Composer stage (optional for PHP dependencies)
# ============================
FROM composer:2 AS backend

WORKDIR /app

# Copy composer files for caching
# COPY composer.json composer.lock ./
# RUN composer install --optimize-autoloader --no-interaction --prefer-dist

# Copy PHP source files
COPY ./*.php ./

# ============================
# 3. Runtime PHP-FPM stage
# ============================
FROM php:8.2-fpm

WORKDIR /var/www/html

# Install PHP extensions needed
RUN apt-get update && apt-get install -y \
    git unzip libpq-dev \
    && docker-php-ext-install pdo pdo_mysql \
    && rm -rf /var/lib/apt/lists/*

# Copy PHP files + vendor from backend stage
COPY --from=backend /app ./

# Copy built Tailwind CSS + JS from frontend stage
# COPY --from=frontend /app/public/css/output.css ./public/css/output.css
# COPY --from=frontend /app/public/js ./public/js

# Copy other public files (images, etc.)
COPY public/ ./public

EXPOSE 9002
CMD ["php-fpm"]
