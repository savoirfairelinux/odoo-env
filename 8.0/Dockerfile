FROM ubuntu:14.04
MAINTAINER <support@savoirfairelinux.com> Savoir-faire Linux Inc.

RUN set -x; \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        gcc \
        patch \
        xz-utils \
        postgresql-client \
        python-dev \
	curl \
	git \
	ca-certificates \
        libxml2-dev \
        libxslt1-dev \
        libgeoip-dev \
        zlib1g-dev \
        libjpeg-dev \
        libpq-dev \
        libsasl2-dev \
        libldap2-dev \
        libyaml-dev \
        fontconfig \
        freetds-dev \
        npm && \
        npm install -g less less-plugin-clean-css

RUN set -x; \
    curl -SL http://download.gna.org/wkhtmltopdf/0.12/0.12.3/wkhtmltox-0.12.3_linux-generic-amd64.tar.xz | \
    tar xJ --strip-components=2 -C /usr/local/bin/ wkhtmltox/bin/wkhtmltopdf && \
    rm -f wkhtmltopdf.tar.xz

RUN set -x; \
    curl -o get-pip.py -SL https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    curl -o requirements.txt -SL https://github.com/odoo/odoo/raw/8.0/requirements.txt && \
    pip install -r requirements.txt && \
    rm -f requirements.txt

RUN set -x; \
    mkdir /opt/odoo8dev && \
    useradd -d /opt/odoo8dev odoo8dev && \
    chown -R odoo8dev /opt/odoo8dev

USER odoo8dev

EXPOSE 9999 8069 8071 8072

WORKDIR /opt/odoo8dev/
