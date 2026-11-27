
# ==========================================
# STAGE 1: BASE PHP ENVIRONMENT
# ==========================================
FROM php:8.4-fpm AS base

WORKDIR /var/www/html

# Install system libraries and PHP extensions
RUN apt-get update && apt-get install -y \
    libzip-dev \
    unzip \
    && docker-php-ext-install \
        pdo_mysql \
        zip \
        bcmath \
        opcache \
    && rm -rf /var/lib/apt/lists/*


# ==========================================
# STAGE 2: DEVELOPMENT
# ==========================================
FROM base AS development

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Enable development PHP configuration
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"


# ==========================================
# STAGE 3: PRODUCTION
# ==========================================
FROM base AS production

# Install Composer temporarily
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Copy Composer dependency files
COPY composer.json composer.lock ./

# Install production dependencies without running scripts
RUN composer install \
    --no-dev \
    --no-interaction \
    --prefer-dist \
    --optimize-autoloader \
    --no-scripts

# Copy Laravel application
COPY . .

# Generate optimized autoload files and discover Laravel packages
RUN composer dump-autoload --no-dev --optimize

# Create and configure Laravel writable directories
RUN mkdir -p \
    storage/framework/cache \
    storage/framework/sessions \
    storage/framework/views \
    storage/logs \
    bootstrap/cache \
    && chown -R www-data:www-data storage bootstrap/cache \
    && chmod -R 775 storage bootstrap/cache

# Enable production PHP configuration
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

# Remove Composer from final image
RUN rm -f /usr/bin/composer
