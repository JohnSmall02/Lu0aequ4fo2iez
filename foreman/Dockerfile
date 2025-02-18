FROM debian:bookworm

LABEL MAINTAINER="John Small"

USER 0

COPY requirements.txt /

RUN apt-get -y update && \
    apt-get -y --no-install-recommends install ca-certificates sshpass unzip gettext libldap2-dev libsasl2-dev ldap-utils git vim wget curl apt-transport-https python3 python3-pip python3-pip-whl python3-ldap

RUN \
    wget --no-check-certificate https://deb.theforeman.org/foreman.asc -O /etc/apt/trusted.gpg.d/foreman.asc && \
    echo "deb http://deb.theforeman.org/ bookworm 3.13" > /etc/apt/sources.list.d/foreman.list && \
    echo "deb http://deb.theforeman.org/ plugins 3.13" >> /etc/apt/sources.list.d/foreman.list && \
    apt-get -y update && \
    pip config set global.require-virtualenv False && \
    pip install --no-cache-dir --break-system-packages -r /requirements.txt

RUN \
    apt-get -y install foreman foreman-postgresql ruby-foreman-ansible foreman-installer && \
    apt-get purge -y --auto-remove && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -f /etc/foreman/database.yml /etc/foreman/settings.yaml && \
    ln -sf ./config/database.yml /etc/foreman/database.yml && \
    ln -sf ./config/settings.yaml /etc/foreman/settings.yaml


ARG HOME=/home/foreman

WORKDIR ${HOME}

COPY entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]

USER 1001

# Start the main process.
CMD bundle exec bin/rails server

EXPOSE 3000/tcp
EXPOSE 5910-5930/tcp
