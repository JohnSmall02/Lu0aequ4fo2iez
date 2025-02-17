FROM debian:bookworm

LABEL MAINTAINER="John Small"

USER 0

COPY requirements.txt /

RUN \
    mkdir -p /etc/apt/trusted.gpg.d && \
    mkdir -p /etc/apt/sources.list.d && \
    wget --no-check-certificate https://deb.theforeman.org/foreman.asc -O /etc/apt/trusted.gpg.d/foreman.asc && \
    echo "deb http://deb.theforeman.org/ bookworm 3.13" > /etc/apt/sources.list.d/foreman.list && \
    echo "deb http://deb.theforeman.org/ plugins 3.13" >> /etc/apt/sources.list.d/foreman.list

RUN apt-get -y update && \
    apt-get -y --no-install-recommends install ca-certificates sshpass unzip gettext libldap2-dev libsasl2-dev ldap-utils git vim wget curl apt-transport-https python3 python3-pip python3-pip-whl python3-ldap

RUN apt-get -y update && \
    pip install --no-cache-dir -r /requirements.txt

RUN \
    apt-get -y install foreman foreman-postgresql ruby-foreman-ansible && \
    apt-get purge -y --auto-remove && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    echo "-- DB config --" && \
    cat /usr/share/foreman/config/database.yml.example && \
    echo "-- config --" && \
    cat /etc/foreman/settings.yaml && \
    echo "-- end config --"

ARG HOME=/home/foreman

WORKDIR ${HOME}
RUN groupadd -r foreman -f -g 0 && \
    useradd -u 1001 -r -g foreman -d $HOME -s /sbin/nologin \
    -c "Foreman Application User" foreman && \
    chown -R 1001:0 $HOME && \
    chmod -R g=u ${HOME}

COPY entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
    
USER 1001

# Start the main process.
CMD bundle exec bin/rails server

EXPOSE 3000/tcp
EXPOSE 5910-5930/tcp
