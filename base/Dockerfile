FROM php:7.1-apache
LABEL maintainer="Michael Gauthier <mike@silverorange.com>"

# Add a flag to know if we are in a container.
ENV DOCKER_ENV=1

# Install base extensions
RUN apt-get update && apt-get install -y \
  aspell              \
  aspell-en           \
  gettext             \
  libbz2-dev          \
  libexif-dev         \
  libfreetype6-dev    \
  libicu-dev          \
  libjpeg62-turbo-dev \
  libmcrypt-dev       \
  libpng12-dev        \
  libpq-dev           \
  libpspell-dev       \
  libsnmp-dev         \
  libtidy-dev         \
  libxml2-dev         \
  libxslt1-dev        \
  libzip-dev          \
  snmp

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install bcmath
RUN docker-php-ext-install bz2
RUN docker-php-ext-install calendar
RUN docker-php-ext-install exif
RUN docker-php-ext-install gettext
RUN docker-php-ext-install gd
RUN docker-php-ext-install intl
RUN docker-php-ext-install mcrypt
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install pcntl
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install pdo_pgsql
RUN docker-php-ext-install pgsql
RUN docker-php-ext-install pspell
RUN docker-php-ext-install snmp
RUN docker-php-ext-install soap
RUN docker-php-ext-install tidy
RUN docker-php-ext-install xmlrpc
RUN docker-php-ext-install xsl
RUN docker-php-ext-install zip

# Install PECL extensions
RUN apt-get update && apt-get install -y \
  imagemagick \
  libgeoip-dev \
  libmagickwand-dev \
  libmemcached-dev \
  libmsgpack-dev \
  librabbitmq-dev \
  zlib1g-dev

RUN pecl install amqp-1.9.3
RUN pecl install geoip-1.1.1
RUN pecl install igbinary-2.0.5
RUN pecl install imagick-3.4.3
RUN pecl install memcached-3.0.4
RUN pecl install msgpack-2.0.2
RUN pecl install redis-3.1.6
# RUN pecl install uploadprogress-1.0.3.1
RUN pecl install xdebug-2.6.0

RUN docker-php-ext-enable \
  amqp \
  geoip \
  igbinary \
  imagick \
  memcached \
  msgpack \
  redis \
  xdebug

# Install Composer
RUN echo "memory_limit=-1" > "$PHP_INI_DIR/conf.d/memory-limit.ini" \
  && echo "date.timezone=${PHP_TIMEZONE:-UTC}" > "$PHP_INI_DIR/conf.d/date_timezone.ini"

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /so/composer
ENV COMPOSER_VERSION 1.6.4

RUN curl -s -f -L -o /tmp/installer.php https://raw.githubusercontent.com/composer/getcomposer.org/b107d959a5924af895807021fcef4ffec5a76aa9/web/installer \
 && php -r " \
    \$signature = '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061'; \
    \$hash = hash('SHA384', file_get_contents('/tmp/installer.php')); \
    if (!hash_equals(\$signature, \$hash)) { \
        unlink('/tmp/installer.php'); \
        echo 'Integrity check failed, installer is either corrupt or worse.' . PHP_EOL; \
        exit(1); \
    }" \
 && php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer --version=${COMPOSER_VERSION} \
 && composer --ansi --version --no-interaction \
 && rm -rf /tmp/* /tmp/.htaccess
RUN groupadd --gid 1000 node \
  && useradd --uid 1000 --gid node --shell /bin/bash --create-home node

# Install Node and Yarn
# gpg keys listed at https://github.com/nodejs/node#release-team
RUN set -ex \
  && for key in \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    56730D5401028683275BD23C23EFEFE93C4CFFFE \
    77984A986EBC2AA786BC0F66B01FBB92821C587A \
  ; do \
    gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
    gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
    gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
  done

ENV NODE_VERSION 8.11.1

RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='x64';; \
    ppc64el) ARCH='ppc64le';; \
    s390x) ARCH='s390x';; \
    arm64) ARCH='arm64';; \
    armhf) ARCH='armv7l';; \
    i386) ARCH='x86';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz" \
  && curl -SLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-$ARCH.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-$ARCH.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
  && rm "node-v$NODE_VERSION-linux-$ARCH.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs

ENV YARN_VERSION 1.6.0

RUN set -ex \
  && for key in \
    6A010C5166006599AA17F08146C2130DFD2497F5 \
  ; do \
    gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
    gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
    gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
  done \
  && curl -fSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz" \
  && curl -fSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz.asc" \
  && gpg --batch --verify yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz \
  && mkdir -p /opt \
  && tar -xzf yarn-v$YARN_VERSION.tar.gz -C /opt/ \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
  && rm yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz

# Install lessc globally (required by concentrate)
RUN yarn global add less

# Install git (needed for installing Composer dev branches)
RUN apt-get update && apt-get install -y git

# Enable needed Apache modules
RUN a2enmod rewrite
RUN a2enmod headers
