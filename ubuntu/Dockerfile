FROM php:7.4-fpm

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

RUN pecl install redis-5.1.1 \
    && pecl install xdebug-2.8.1 \
    && docker-php-ext-enable redis xdebug

RUN apt-get update && apt-get install -y git wget libicu-dev

RUN apt-get update && apt-get install -y libicu-dev

RUN docker-php-ext-install intl && docker-php-ext-enable intl

RUN docker-php-ext-install pdo && docker-php-ext-enable pdo

# composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php -r "if (hash_file('sha384', 'composer-setup.php') === '795f976fe0ebd8b75f26a6dd68f78fd3453ce79f32ecb33e7fd087d39bfeb978342fb73ac986cd4f54edd0dc902601dc') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');"
    # mv composer.phar /usr/local/bin/composer

# symfony_installer
RUN wget https://get.symfony.com/cli/installer -O - | bash
    # + cp /root/.symfony/bin/symfony /usr/local/bin/symfony

RUN apt-get update && apt-get install -y libzip-dev
RUN docker-php-ext-install zip && docker-php-ext-enable zip

RUN apt-get update && apt-get install -y libxslt-dev
RUN docker-php-ext-install xsl && docker-php-ext-enable xsl

RUN apt-get update && apt-get install -y libpq-dev
RUN docker-php-ext-install pdo_pgsql && docker-php-ext-enable pdo_pgsql

### ### ### ###
### cd amqp-1.0.7   && \ # replace this with the current version
### ### ### ###
# checking for amqp using pkg-config... configure: error: librabbitmq not found
RUN apt-get update && apt-get install -y librabbitmq-dev

RUN wget http://pecl.php.net/get/amqp -O amqp.tar.gz && \
tar -zxvf amqp.tar.gz && \
cd amqp-1.10.2 && \
phpize  && \
./configure --with-amqp && \
make && \
make install

RUN docker-php-ext-enable amqp

RUN mv composer.phar /usr/local/bin/composer

RUN cp /root/.symfony/bin/symfony /usr/local/bin/symfony

# RUN mkdir /root/.ssh
COPY authorized_keys /root/.ssh/authorized_keys
RUN chmod 0600 /root/.ssh/authorized_keys && chown root:root /root/.ssh/authorized_keys && chmod 0600 /root/.ssh

RUN apt-get update && apt-get install -y ssh openssh-server
RUN mkdir /var/run/sshd

RUN echo 'root:secret' | chpasswd
RUN sed -i 's/#*PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
RUN sed -i 's/#*LogLevel INFO/LogLevel DEBUG/g' /etc/ssh/sshd_config
RUN sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config
RUN sed -i 's/#PermitTunnel no/PermitTunnel yes/g' /etc/ssh/sshd_config
RUN sed -i 's/#AllowAgentForwarding yes/AllowAgentForwarding yes/g' /etc/ssh/sshd_config
RUN sed -i 's/#Port 22/Port 19922/g' /etc/ssh/sshd_config
RUN sed -i 's/#AddressFamily any/AddressFamily any/g' /etc/ssh/sshd_config


WORKDIR /mnt

#EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
#CMD ["sleep", "infinity"]

