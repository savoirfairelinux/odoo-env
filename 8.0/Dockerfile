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
    patch \
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
	${dev_packages} \
        npm && \
        ln -s /usr/bin/nodejs /usr/bin/node && \
        npm install -g less less-plugin-clean-css && \
        cd /tmp && \
        curl -o get-pip.py -SL https://bootstrap.pypa.io/get-pip.py && \
        python get-pip.py && \
        pip install urllib3[secure] && \
	curl -o requirements.txt -SL https://github.com/odoo/odoo/raw/${VERSION}/requirements.txt && \
	pip install -r requirements.txt && \
	cd /fake_buildout && \
        curl -o bootstrap-buildout.py -SL https://bootstrap.pypa.io/bootstrap-buildout.py && \
        curl -o requirements.txt -SL https://github.com/odoo/odoo/raw/${VERSION}/requirements.txt && \
        awk -F== '{print "     " $1}' requirements.txt >> buildout.cfg && \
        sed -i 's|==| = |' requirements.txt && \
        echo "\n[versions]\nnum2words = 0.5.3\ngeoip = 1.3.2\nsphinx = 1.3.1\nanybox.recipe.odoo = 1.9.2\ncp.recipe.cmd = 0.5\nzope.testing = 4.5.0\nzope.interface = 4.1.3\nzope.exceptions = 4.0.8\nz3c.recipe.filetemplate = 2.2.0\n" >> buildout.cfg && \
        cat requirements.txt >> buildout.cfg && \
        cat buildout.cfg && \
        python bootstrap-buildout.py && \
        bin/buildout && \
        mkdir -p /opt/${USER}/.buildout && \
	mv eggs /opt/${USER}/.buildout/ && \
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
    mkdir /opt/${USER}/git && \
    useradd -d /opt/${USER} ${USER} && \
    chown -R ${USER}:${USER} /opt/${USER}

USER ${USER}

WORKDIR /opt/${USER}/

RUN echo "[buildout]\neggs-directory = /opt/${USER}/.buildout/eggs\n" > .buildout/default.cfg && \
    ls -la

EXPOSE 8069 8071
