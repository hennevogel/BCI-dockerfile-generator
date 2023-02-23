# SPDX-License-Identifier: MIT
#!BuildTag: bci/php:8.0.27-fpm
#!BuildTag: bci/php:8.0.27-fpm-%RELEASE%
#!BuildTag: bci/php:8-fpm
#!BuildTag: bci/php:8-fpm-%RELEASE%
#!BuildVersion: 15.4.8
FROM suse/sle15:15.4

MAINTAINER SUSE LLC (https://www.suse.com/)

# Define labels according to https://en.opensuse.org/Building_derived_containers
# labelprefix=com.suse.bci.php
LABEL org.opencontainers.image.title="SLE BCI PHP 8 fpm Container Image"
LABEL org.opencontainers.image.description="PHP 8 fpm based on the SLE Base Container Image."
LABEL org.opencontainers.image.version="8"
LABEL org.opencontainers.image.url="https://www.suse.com/products/server/"
LABEL org.opencontainers.image.created="%BUILDTIME%"
LABEL org.opencontainers.image.vendor="SUSE LLC"
LABEL org.opensuse.reference="registry.suse.com/bci/php:8-%RELEASE%"
LABEL org.openbuildservice.disturl="%DISTURL%"
LABEL com.suse.supportlevel="techpreview"
LABEL com.suse.eula="sle-bci"
LABEL com.suse.lifecycle-url="https://www.suse.com/lifecycle"
LABEL com.suse.image-type="sle-bci"
LABEL com.suse.release-stage="released"
# endlabelprefix


WORKDIR /usr/src/
#!RemoteAssetUrl: https://secure.php.net/distributions/php-8.0.27.tar.xz
COPY php-8.0.27.tar.xz .
#!RemoteAssetUrl: https://secure.php.net/distributions/php-8.0.27.tar.xz.asc
COPY php-8.0.27.tar.xz.asc .
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
       php8-fpm \
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

ENV PHP_URL="https://secure.php.net/distributions/php-8.0.27.tar.xz" PHP_ASC_URL="https://secure.php.net/distributions/php-8.0.27.tar.xz.asc"
ENV PHP_SHA256="f942cbfe2f7bacbb8039fb79bbec41c76ea779ac5c8157f21e1e0c1b28a5fc3a"
ENV GPG_KEYS=""
ENV PHP_INI_DIR="/etc/php8/"
ENV PHPIZE_DEPS="php8-devel autoconf gawk gcc make file"
ENV PHP_VERSION="8.0.27"
ENV COMPOSER_VERSION="%%composer_version%%"
ENV PHP_CFLAGS="-O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -O3 -fPIE -fPIC -DPIC -D_GNU_SOURCE -fno-strict-aliasing"
ENV PHP_CPPFLAGS="-O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -O3 -fPIE -fPIC -DPIC -D_GNU_SOURCE -fno-strict-aliasing"
ENV PHP_LDFLAGS="-pie"

ENTRYPOINT ["docker-php-entrypoint"]
CMD ["php-fpm"]


WORKDIR /srv/www/htdocs

RUN set -eux; \
	cd /etc/php8/fpm/; \
        cp php-fpm.d/www.conf.default php-fpm.d/www.conf; \
        cp php-fpm.conf.default php-fpm.conf; \
	{ \
		echo '[global]'; \
		echo 'error_log = /proc/self/fd/2'; \
		echo; echo '; https://github.com/docker-library/php/pull/725#issuecomment-443540114'; echo 'log_limit = 8192'; \
		echo; \
		echo '[www]'; \
		echo '; if we send this to /proc/self/fd/1, it never appears'; \
		echo 'access.log = /proc/self/fd/2'; \
		echo; \
		echo 'clear_env = no'; \
		echo; \
		echo '; Ensure worker stdout and stderr are sent to the main error log.'; \
		echo 'catch_workers_output = yes'; \
		echo 'decorate_workers_output = no'; \
	} | tee php-fpm.d/docker.conf; \
	{ \
		echo '[global]'; \
		echo 'daemonize = no'; \
	} | tee php-fpm.d/zz-docker.conf

# Override stop signal to stop process gracefully
# https://github.com/php/php-src/blob/17baa87faddc2550def3ae7314236826bc1b1398/sapi/fpm/php-fpm.8.in#L163
STOPSIGNAL SIGQUIT

EXPOSE 9000
