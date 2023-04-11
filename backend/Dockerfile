# Use the official PHP image with Nginx
FROM php:8.2-fpm

# Install necessary PHP extensions and tools
RUN apt-get update && apt-get install -y \
        libzip-dev \
        git \
        unzip \
    && pecl install mongodb \
    && docker-php-ext-enable mongodb \
    && docker-php-ext-install zip

# Install composer
COPY --from=composer /usr/bin/composer /usr/bin/composer

# Create a non-root user and set up the working directory
RUN useradd -ms /bin/bash nonrootuser \
    && mkdir -p /var/www/html \
    && chown -R nonrootuser:nonrootuser /var/www/html

# Set the working directory
WORKDIR /var/www/html

# Copy project files to the container
COPY . .

# Change ownership of copied files
RUN chown -R nonrootuser:nonrootuser /var/www/html

# Set a different directory for installing dependencies
ENV COMPOSER_HOME /tmp/composer

# Run composer install as non-root user with --no-scripts flag
USER nonrootuser
RUN composer install --no-interaction --no-scripts

# Copy the installed dependencies to the /var/www/html/vendor folder
USER root
RUN cp -r /tmp/composer/vendor /var/www/html/vendor
RUN chown -R nonrootuser:nonrootuser /var/www/html/vendor