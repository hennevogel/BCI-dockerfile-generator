# SPDX-License-Identifier: MIT
#!BuildTag: bci/php:8.0.28-apache
#!BuildTag: bci/php:8.0.28-apache-%RELEASE%
#!BuildTag: bci/php:8-apache
#!BuildTag: bci/php:8-apache-%RELEASE%
#!BuildVersion: 15.4.8
FROM suse/sle15:15.4

MAINTAINER SUSE LLC (https://www.suse.com/)

# Define labels according to https://en.opensuse.org/Building_derived_containers
# labelprefix=com.suse.bci.php
LABEL org.opencontainers.image.title="SLE BCI PHP 8 apache Container Image"
LABEL org.opencontainers.image.description="PHP 8 apache based on the SLE Base Container Image."
LABEL org.opencontainers.image.version="8"
LABEL org.opencontainers.image.url="https://www.suse.com/products/server/"
LABEL org.opencontainers.image.created="%BUILDTIME%"
LABEL org.opencontainers.image.vendor="SUSE LLC"
LABEL org.opencontainers.image.source="https://github.com/SUSE/BCI-dockerfile-generator/tree/sle15-sp4/php-8-apache-image"
LABEL org.opensuse.reference="registry.suse.com/bci/php:8-%RELEASE%"
LABEL org.openbuildservice.disturl="%DISTURL%"
LABEL com.suse.supportlevel="techpreview"
LABEL com.suse.eula="sle-bci"
LABEL com.suse.lifecycle-url="https://www.suse.com/lifecycle"
LABEL com.suse.image-type="sle-bci"
LABEL com.suse.release-stage="released"
# endlabelprefix


WORKDIR /usr/src/
#!RemoteAssetUrl: https://secure.php.net/distributions/php-8.0.28.tar.xz
COPY php-8.0.28.tar.xz .
#!RemoteAssetUrl: https://secure.php.net/distributions/php-8.0.28.tar.xz.asc
COPY php-8.0.28.tar.xz.asc .
COPY README.macros .
COPY php-build-reproducible-phar.patch .
COPY php-date-regenerate-lexers.patch .
COPY php-crypt-tests.patch .
COPY php-sort-filelist-phar.patch .
COPY php-systemd-unit.patch .
COPY php-systzdata-v20.patch .
COPY php-ini.patch .
COPY php-php-config.patch .
COPY php-ar-flags.patch .
COPY php-phpize.patch .
COPY docker-php-source docker-php-entrypoint docker-php-ext-configure docker-php-ext-enable docker-php-ext-install /usr/local/bin/
RUN chmod +x /usr/local/bin/docker-php-*

RUN set -euo pipefail; \
    zypper -n in --no-recommends \
       php8-cli \
       apache2-mod_php8 \
       php-composer \
       php8-curl \
       php8-zip \
       php8-zlib \
       php8-phar \
       php8-mbstring \
       curl \
       git-core \
       distribution-release \
       tar \
       xz \
       patch \
       sed \
       find \
       binutils; \
    zypper -n clean; \
    rm -rf /var/log/*

ENV PHP_URL="https://secure.php.net/distributions/php-8.0.28.tar.xz" PHP_ASC_URL="https://secure.php.net/distributions/php-8.0.28.tar.xz.asc"
ENV PHP_SHA256="5e07278a1f315a67d36a676c01343ca2d4da5ec5bdb15d018e4248b3012bc0cd"
ENV GPG_KEYS=""
ENV PHP_INI_DIR="/etc/php8/"
ENV PHPIZE_DEPS="php8-devel autoconf gawk gcc make file"
ENV PHP_VERSION="8.0.28"
ENV COMPOSER_VERSION="%%composer_version%%"
ENV PHP_CFLAGS="-O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -O3 -fPIE -fPIC -DPIC -D_GNU_SOURCE -fno-strict-aliasing"
ENV PHP_CPPFLAGS="-O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -O3 -fPIE -fPIC -DPIC -D_GNU_SOURCE -fno-strict-aliasing"
ENV PHP_LDFLAGS="-pie"

ENTRYPOINT ["docker-php-entrypoint"]
CMD ["apache2-foreground"]



ENV APACHE_CONFDIR /etc/apache2
ENV APACHE_ENVVARS /usr/sbin/envvars

STOPSIGNAL SIGWINCH

# create our own apache2-foreground from the systemd startup script
RUN cat /usr/sbin/start_apache2 | sed 's|^exec $apache_bin|exec $apache_bin -DFOREGROUND|' > /usr/local/bin/apache2-foreground
RUN chmod +x /usr/local/bin/apache2-foreground

# apache fails to start without its log folder
RUN mkdir -p /var/log/apache2

WORKDIR /srv/www/htdocs

EXPOSE 80
