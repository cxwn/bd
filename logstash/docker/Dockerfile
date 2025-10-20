ARG BASE=swr.cn-north-1.myhuaweicloud.com/cxwn/openanolis/anolisos:8.8
FROM ${BASE}

ARG TARGETARCH="amd64" \
    VERSION="9.1.5"

# https://artifacts.elastic.co/downloads/logstash/logstash-9.1.5-linux-x86_64.tar.gz
ADD ["logstash-${VERSION}-linux-x86_64.tar.gz", "/usr/local/"]

COPY \ 
    config/pipelines.yml config/log4j2.properties config/log4j2.file.properties /usr/local/logstash/config/
COPY \
    config/logstash-full.yml /usr/share/logstash/config/logstash.yml
COPY \ 
    pipeline/default.conf /usr/share/logstash/pipeline/logstash.conf

COPY \
    docker-entrypoint /usr/local/bin/

COPY \
    env2yaml/env2yaml-${TARGETARCH} /usr/local/bin/env2yaml

RUN set -eux; \
    mv "/usr/local/logstash-${VERSION}" /usr/local/logstash; \
    groupadd -g 1000 logstash; \
    useradd -u 1000 -g 1000 -d /usr/local/logstash -M logstash; \
    chown --recursive logstash:logstash /usr/local/logstash/; \
    chown -R logstash:root /usr/local/logstash; \
    chmod -R g=u /usr/local/logstash; \
    mkdir /licenses/; \
    mv /usr/local/logstash/NOTICE.TXT /licenses/NOTICE.TXT; \
    mv /usr/local/logstash/LICENSE.txt /licenses/LICENSE.txt; \
    find /usr/local/logstash -type d -exec chmod g+s {} \; \
    chown --recursive logstash:root config/ pipeline/; \
    chmod 0755 /usr/local/bin/entrypoint.sh; \
    chmod 0755 /usr/local/bin/env2yaml

FROM \
    ${BASE}

COPY --from=0 /usr/local/ /usr/local/

ARG VERSION=9.1.5

LABEL AUTHOR="CXWN" \
      MAINTAINER="CXWN" \
      DESCRIPTION="Logstash ${VERSION} for AnolisOS" \
      VERSION="${VERSION}" \
      ARCH="${TARGETARCH}"

ENV TZ=Asia/Shanghai \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    ELASTIC_CONTAINE=true \
    PATH=/usr/local/logstash/bin:$PATH

RUN set -eux; \
    groupadd -g 1000 logstash; \
    useradd -u 1000 -g 1000 -d /usr/local/logstash -M logstash; \
    chown --recursive logstash:logstash /usr/local/logstash/

WORKDIR \
    /usr/local/logstash

USER 1000

EXPOSE 9600 5044

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]