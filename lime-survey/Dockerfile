# Utilizar la imagen base de PHP con Apache
FROM php:8.2-apache

ENV DOWNLOAD_URL=https://download.limesurvey.org/latest-master/limesurvey6.6.3+240909.zip

# Instalar extensiones necesarias para LimeSurvey
RUN apt-get update && apt-get install -y unzip libc-client-dev libfreetype6-dev libmcrypt-dev libpng-dev libjpeg-dev libldap-common libldap2-dev zlib1g-dev libkrb5-dev libtidy-dev libzip-dev libsodium-dev libpq-dev libonig-dev netcat-openbsd && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-configure gd --with-freetype=/usr/include/  --with-jpeg=/usr \
    && docker-php-ext-install gd mysqli mbstring pgsql pdo pdo_mysql pdo_pgsql opcache zip iconv tidy \
    && docker-php-ext-configure ldap --with-libdir=lib/$(gcc -dumpmachine)/ \
    && docker-php-ext-install ldap \
    && docker-php-ext-configure imap --with-imap-ssl --with-kerberos \
    && docker-php-ext-install imap \
    && docker-php-ext-install sodium \
    && pecl install mcrypt-1.0.6 \
    && docker-php-ext-enable mcrypt \
    && docker-php-ext-install exif

# Habilitar mod_rewrite para Apache
RUN a2enmod rewrite
RUN a2enmod remoteip

RUN { \
        echo 'opcache.memory_consumption=128'; \
        echo 'opcache.interned_strings_buffer=8'; \
        echo 'opcache.max_accelerated_files=4000'; \
        echo 'opcache.revalidate_freq=2'; \
        echo 'opcache.fast_shutdown=1'; \
        echo 'opcache.enable_cli=1'; \
    } > /usr/local/etc/php/conf.d/opcache-recommended.ini

# Descargar y extraer LimeSurvey desde el archivo .zip
RUN curl -L "${DOWNLOAD_URL}" -o limesurvey.zip \
    && unzip limesurvey.zip -d /var/www/html \
    && rm limesurvey.zip

# Configurar permisos adecuados
RUN chown -R www-data:www-data /var/www/html/limesurvey/ \
    && chmod -R 755 /var/www/html/limesurvey/

#Accept remote ip from local proxies where X-Forwarded-For set
RUN { \
        echo 'RemoteIPHeader X-Forwarded-For'; \
        echo 'RemoteIPInternalProxy 10.0.0.0/8 127.0.0.1'; \
    } > /etc/apache2/conf-enabled/remoteip.conf


# Exponer el puerto 80
EXPOSE 80

# Comando para iniciar Apache en el contenedor
CMD ["apache2-foreground"]
