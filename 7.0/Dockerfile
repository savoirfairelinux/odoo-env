FROM ubuntu:14.04
MAINTAINER <support@savoirfairelinux.com> Savoir-faire Linux Inc.

ENV USER odoo
ENV VERSION 8.0
ENV dev_packages python-dev \
    libxml2-dev \
    libxslt1-dev \
    zlib1g-dev \
    libjpeg-dev \
    libpq-dev \
    libsasl2-dev \
    libldap2-dev \
    libyaml-dev \
    freetds-dev \
    libffi-dev \
    libgeoip-dev \
    gcc \
    make

RUN set -x; \
    locale-gen en_US.UTF-8 && \
    update-locale && \
    echo 'LANG="en_US.UTF-8"' > /etc/default/locale

RUN set -x; \
    apt-get update && \
    apt-get install -y curl && \
    echo "deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main" > /etc/apt/sources.list.d/pgdg.list && \
    curl -SL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

COPY buildout.cfg /fake_buildout/

RUN set -x; mkdir -p /root/.buildout && mkdir -p /opt/${USER}/.buildout

COPY default.cfg /root/.buildout/

RUN set -x; \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
    ca-certificates \
        xz-utils \
        postgresql-client-9.5 \
    fontconfig \
    libxrender1 \
    libxext6 \
    patch \
    curl \
    bzip2 \
    ssh \
    ${dev_packages} \
        npm && \
    ln -s /usr/bin/nodejs /usr/bin/node && \
    npm install -g less less-plugin-clean-css

RUN cd /fake_buildout && \
    curl -o bootstrap-buildout.py -SL https://bootstrap.pypa.io/bootstrap-buildout.py && \
    echo "\n[versions]\n" >> buildout.cfg && \
    python2.7 bootstrap-buildout.py && \
    bin/buildout && \
    curl -o get-pip.py -SL https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    pip install urllib3[secure] && \
    cd .. && \
    rm -rf fake_buildout && \
    apt-get -y remove ${dev_packages} && \
    apt-get clean all

RUN set -x; \
    curl -SL http://download.gna.org/wkhtmltopdf/0.12/0.12.3/wkhtmltox-0.12.3_linux-generic-amd64.tar.xz | \
    tar xJ --strip-components=2 -C /usr/local/bin/ wkhtmltox/bin/wkhtmltopdf

RUN set -x; \
    curl -SL https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-2.1.1-linux-x86_64.tar.bz2 | \
    tar xj --strip-components=2 -C /usr/local/bin/ phantomjs-2.1.1-linux-x86_64/bin/phantomjs

RUN set -x; \
    mv /root/.buildout/default.cfg /opt/${USER}/.buildout/ && \
    mkdir /opt/${USER}/git && \
    useradd -d /opt/${USER} ${USER} && \
    chown -R ${USER}:${USER} /opt/${USER}

USER ${USER}

WORKDIR /opt/${USER}/

EXPOSE 8069 8071
