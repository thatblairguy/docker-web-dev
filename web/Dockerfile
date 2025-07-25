FROM ubuntu:24.04

# Update the package manager...
# Install add-apt-repository
# Add the PHP repository
# ...then install packages.
RUN     DEBIAN_FRONTEND=noninteractive  apt-get update -y;                                \
        DEBIAN_FRONTEND=noninteractive  apt-get install -yq software-properties-common;  \
        DEBIAN_FRONTEND=noninteractive  add-apt-repository ppa:ondrej/php;               \
        DEBIAN_FRONTEND=noninteractive  add-apt-repository ppa:ondrej/apache2;           \
        DEBIAN_FRONTEND=noninteractive  apt-get install -yq                              \
        apache2                 \
        build-essential         \
        php8.4                  \
        libapache2-mod-php8.4   \
        php8.4-bz2              \
        php8.4-cli              \
        php8.4-common           \
        php8.4-curl             \
        php8.4-fpm              \
        php8.4-gd               \
        php8.4-mbstring         \
        php8.4-memcached        \
        php8.4-imagick          \
        php8.4-mysql            \
        php8.4-oauth            \
        php8.4-opcache          \
        php8.4-readline         \
        php8.4-sqlite3          \
        php8.4-soap             \
        php8.4-xdebug           \
        php8.4-xml              \
        mariadb-client          \
        curl                    \
        git                     \
        imagemagick             \
        vim                     \
        zip

# Copy up the available sites config. The startup script
# manipulates this file based on env vars.
COPY ./build/000-default.conf /etc/apache2/sites-available/000-default.conf

# ## These are the PHP modules that are available to be
# ## loaded by the CLI & apache. This should be inline with
# ## the modules installed above.
# ##
# ## Files in the build directory are for PHP 7.3, not the 7.4
# ## installed above.  Fix that befor enabling this.
# ##
# COPY ./build/php-conf /etc/php/7.3/mods-available

RUN a2enmod include
RUN a2enmod proxy
RUN a2enmod proxy_http
RUN a2enmod rewrite

# Forward logs to docker log collector.
RUN ln -sf /dev/stdout /var/log/apache2/access.log &&               \
    ln -sf /dev/stderr /var/log/apache2/error.log &&                \
    ln -sf /dev/stdout /var/log/apache2/000_default-access_log &&   \
    ln -sf /dev/stderr /var/log/apache2/000_default-error_log

# Copy our custom entrypoint and make it executable.
COPY ./build/docker-entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

# Install Node
# nvm environment variables
# NVM_DIR="$HOME/.nvm"
#ENV NVM_DIR /root/nvm
# ENV NVM_DIR /usr/local/nvm
# ENV NODE_VERSION 20.13.0

# install nvm
# https://github.com/nvm-sh/nvm
#RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# install node and npm
# RUN . $NVM_DIR/nvm.sh \
#     && nvm install $NODE_VERSION \
#     && nvm alias default $NODE_VERSION \
#     && nvm use default

# # add node and npm to path so the commands are available
# ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
# ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

# # update npm
# RUN npm install -g npm@7.6

## Turn off xdebug as it uses resources that could be best used
## elsewhere. Once the container is up you can use
## phpenmod -s <SAPI> xdebug
## where SAPI is probably, most likely, apache2
RUN phpdismod -s cli xdebug
RUN phpdismod -s apache2 xdebug

# # Expose the default Apache ports.
EXPOSE 80
EXPOSE 443

# Replace the standard entrypoint /bin/sh with our script.
ENTRYPOINT ["docker-entrypoint.sh"]

# If no command is passed to the container, start Apache by default.
CMD ["apachectl", "-D", "FOREGROUND"]

# Default to /var/www so we don't need to do it by hand.
WORKDIR /var/www
