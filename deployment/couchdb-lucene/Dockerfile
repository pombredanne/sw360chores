# Copyright Bosch Software Innovations GmbH, 2016.
# Part of the SW360 Portal Project.
#
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License v1.0
# which accompanies this distribution, and is available at
# http://www.eclipse.org/legal/epl-v10.html

FROM debian:jessie
MAINTAINER admin@sw360.org
ENV DEBIAN_FRONTEND noninteractive

ENV _update="apt-get update"
ENV _install="apt-get install -y --no-install-recommends"
ENV _cleanup="eval apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*"
ENV _purge="apt-get purge -y --auto-remove"

run if [ "$http_proxy" = "None" ]; then unset http_proxy; fi && if [ "$https_proxy" = "None" ]; then unset https_proxy; fi \
 && set -x \
 && echo "deb http://ftp.debian.org/debian jessie-backports main" > /etc/apt/sources.list.d/backports.list \
 && $_update && $_install openjdk-8-jre openjdk-8-jdk git-core unzip \
 && apt-get update --fix-missing && $_install maven \
 && $_cleanup \
 && update-alternatives --set java /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java \
 && echo "JAVA_HOME=\"/usr/lib/jvm/java-8-openjdk-amd64/jre\"" >> /etc/environment

run mkdir -p /couchdb-lucene
WORKDIR /couchdb-lucene
run if [ "$http_proxy" = "None" ]; then unset http_proxy; fi && if [ "$https_proxy" = "None" ]; then unset https_proxy; fi \
 && set -x \
 && git clone https://github.com/rnewson/couchdb-lucene /couchdb-lucene \
 && git checkout v1.1.0 \
 && if [ -z "$http_proxy" ]; then \
        mvn; \
    else \
        mvn -DproxySet=true \
            -DproxyHost=$(echo "$http_proxy" | sed -e 's/.*:\/\/\(.*\):.*/\1/') \
            -DproxyPort=$(echo "$http_proxy" | sed -e 's/.*:\(.*\)/\1/'); \
    fi \
 && unzip -d / target/couchdb-lucene-*.zip \
 && $_purge openjdk-8-jdk git-core unzip maven && $_cleanup \
 && rm -rf /couchdb-lucene \
 && mv /couchdb-lucene-* /couchdb-lucene

EXPOSE 5985

COPY docker-entrypoint.sh /usr/local/bin/entrypoint.sh

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
CMD ["/couchdb-lucene/bin/run"]
