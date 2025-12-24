FROM php:7.4.29-apache

# Enable Apache mod_rewrite
RUN a2enmod rewrite

# Install required system packages (including OpenSSL 1.1.1)
RUN apt-get update && apt-get install -y \
    openssl \
    libssl1.1 \
    && rm -rf /var/lib/apt/lists/*

# Optional: Install common PHP extensions
RUN docker-php-ext-install \
    mysqli \
    pdo \
    pdo_mysql

# Allow .htaccess override (required for mod_rewrite)
RUN sed -i 's/AllowOverride None/AllowOverride All/g' /etc/apache2/apache2.conf

# Set working directory
WORKDIR /var/www/html

# Copy your application code
COPY . /var/www/html/

# Set proper permissions
RUN chown -R www-data:www-data /var/www/html

EXPOSE 80

CMD ["apache2-foreground"]
