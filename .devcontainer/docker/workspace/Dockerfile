FROM ubuntu:22.04

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=${USER_UID}

ARG LOCALE=en_US.UTF-8
ARG TIME_ZONE=UTC

ENV PKG="build-essential curl dnsutils file fonts-ipafont git imagemagick jq locales nfs-client procps rsync shellcheck sqlite3 tree vim wget whois zip zsh"
ENV PKG_GUI="libgtk2.0-0 libgtk-3-0 libnotify-dev libgconf-2-4 libnss3 libxss1 libasound2 libxtst6 xauth xvfb libgbm-dev fonts-ipafont"
ENV PKG_PHP="php-cli php-curl php-fpm php-gd php-imagick php-imap php-intl php-mbstring php-memcached php-mysql php-pgsql php-redis php-sqlite3 php-xml php-zip"
ENV PKG_OTHER="default-jre ruby-full"
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV DEBIAN_FRONTEND noninteractive
ENV LANG=${LOCALE}
ENV TZ=${TIME_ZONE}

COPY --from=composer:2.4.3 /usr/bin/composer /usr/bin/composer

RUN apt-get update \
    && apt-get install -y $PKG $PKG_GUI $PKG_PHP $PKG_OTHER \
    #
    # node.js
    && curl -sL https://deb.nodesource.com/setup_18.x | bash - \
    && apt install -y nodejs \
    #
    # yq
    && wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq \
    && chmod +x /usr/bin/yq \
    #
    # locale
    && sed -i -E "s/# (${LOCALE})/\1/" /etc/locale.gen \
    && locale-gen ${LOCALE} \
    && dpkg-reconfigure locales \
    && update-locale LANG=${LOCALE} \
    #
    # timezone
    && ln -snf /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime && echo ${TIME_ZONE} > /etc/timezone \
    #
    # user
    && groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd -s /bin/bash --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} \
    && apt-get install -y sudo \
    && echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME}
