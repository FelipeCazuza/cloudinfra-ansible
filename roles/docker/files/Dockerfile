FROM php:8.2-apache-bookworm

# ===== Ajustar repositórios HTTPS confiáveis =====
RUN mkdir -p /etc/apt && \
    echo "deb [trusted=yes] https://mirror.aarnet.edu.au/pub/debian bookworm main contrib non-free non-free-firmware" > /etc/apt/sources.list && \
    echo "deb [trusted=yes] https://mirror.aarnet.edu.au/pub/debian bookworm-updates main contrib non-free non-free-firmware" >> /etc/apt/sources.list && \
    echo "deb [trusted=yes] https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware" >> /etc/apt/sources.list && \
    apt-get -o Acquire::ForceIPv4=true -o Acquire::https::Verify-Peer=false -o Acquire::https::Verify-Host=false update

# ===== Dependências GLPI =====
RUN apt-get install -y \
    wget unzip git libpng-dev libjpeg-dev libfreetype6-dev libldap2-dev \
    libxml2-dev zlib1g-dev libzip-dev libicu-dev g++ libbz2-dev libsodium-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install -j$(nproc) gd mysqli pdo pdo_mysql ldap zip intl bz2 exif && \
    docker-php-ext-enable intl bz2 exif ldap zip && \
    rm -rf /var/lib/apt/lists/*

# ===== Baixar GLPI oficial 10.0.15 =====
WORKDIR /var/www/html
RUN wget https://github.com/glpi-project/glpi/releases/download/10.0.15/glpi-10.0.15.tgz && \
    tar -xzf glpi-10.0.15.tgz --strip-components=1 && \
    rm -f glpi-10.0.15.tgz && \
    chown -R www-data:www-data /var/www/html

# ===== Apache/PHP ajustes =====
RUN a2enmod rewrite && \
    echo "date.timezone=America/Sao_Paulo" > /usr/local/etc/php/conf.d/timezone.ini && \
    echo "session.cookie_httponly = On" > /usr/local/etc/php/conf.d/session_security.ini
    #echo "session.cookie_secure = On" >> /usr/local/etc/php/conf.d/session_security.ini

EXPOSE 80
CMD ["apache2-foreground"]