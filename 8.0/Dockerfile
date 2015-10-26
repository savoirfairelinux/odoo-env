FROM buildpack-deps:trusty-scm
MAINTAINER <support@savoirfairelinux.com> Savoir-faire Linux Inc.

RUN set -x; \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        python-dev \
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
        npm install -g less less-plugin-clean-css && \
        curl -o wkhtmltox.deb -SL http://download.gna.org/wkhtmltopdf/0.12/0.12.1/wkhtmltox-0.12.1_linux-trusty-amd64.deb && \
        echo '7dd0e9be7e4fa2a58aa7066460927cdb8ba07492 wkhtmltox.deb' | sha1sum -c - && \
        dpkg --force-depends -i wkhtmltox.deb && \
        apt-get -y install -f --no-install-recommends && \
        rm -rf /var/lib/apt/lists/* wkhtmltox.deb

RUN useradd -d /var/odoo8 odoo8 && \
    mkdir -p /var/odoo8/app && \
    chown -R odoo8 /var/odoo8

USER odoo8

# Run a fake buildout to install depency eggs
RUN mkdir -p /var/odoo8/.buildout/eggs && \
    mkdir /var/odoo8/fake_build
COPY default.cfg /var/odoo8/.buildout/
COPY buildout.cfg /var/odoo8/fake_build/

RUN set -x; \
    cd /var/odoo8/fake_build && \
    curl -o requirements.txt -SL https://github.com/odoo/odoo/raw/8.0/requirements.txt && \
    curl -o bootstrap-buildout.py -SL https://bootstrap.pypa.io/bootstrap-buildout.py && \
    curl -o pydot-1.0.2.tar.gz -SL http://pydot.googlecode.com/files/pydot-1.0.2.tar.gz && \
    tar xzf pydot-1.0.2.tar.gz && \
    python bootstrap-buildout.py && \
    /var/odoo8/fake_build/bin/buildout setup pydot-1.0.2/setup.py bdist_egg && \
    mv pydot-1.0.2/dist/* /var/odoo8/.buildout/eggs/ && \
    /var/odoo8/fake_build/bin/buildout && \
    rm -rf /var/odoo8/fake_build

EXPOSE 8069 8071

WORKDIR /var/odoo8/
