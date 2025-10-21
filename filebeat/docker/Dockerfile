ARG BASE="swr.cn-north-1.myhuaweicloud.com/cxwn/openanolis/anolisos:8.8"

FROM \
    ${BASE}

ARG VERSION="9.1.5"
ARG TARGETARCH="x86_64"
ARG APP="filebeat"

ADD --chown=1000:0 [ "${APP}-${VERSION}-linux-${TARGETARCH}.tar.gz", "/usr/local/share/" ]

COPY \
    [ "tini-amd64", "/usr/local/bin/tini" ]

RUN set -eux; \
    mv /usr/local/share/${APP}-${VERSION}-linux-${TARGETARCH} /usr/local/share/${APP}; \
    mkdir /usr/local/share/${APP}/{data,logs}; \
    chmod -R g=u /usr/local/share/${APP}; \
    chmod -R g+w /usr/local/share/${APP}; \
    chown -R root:root /usr/local/share/${APP}; \
    chmod go-w /usr/local/share/${APP}/${APP}.yml; \
    find /usr/local/share/${APP} -type d -exec chmod 0750 {} \;; \
    find /usr/local/share/${APP} -type f -exec chmod 0640 {} \;; \
    chmod 0750 /usr/local/share/${APP}/${APP}; \
    chmod 0770 /usr/local/share/${APP}/data /usr/local/share/${APP}/logs; \
    chmod +x /usr/local/bin/tini

FROM \
    ${BASE}

ARG VERSION="9.1.5"
ARG TARGETARCH="x86_64"
ARG APP="filebeat"

LABEL \
    AUTHOR="CXWN"\
    VERSION="${VERSION}"\
    BUILD_DATE="2025-10-21"

COPY \
    --from=0  /usr/local/share/${APP} /usr/local/share/${APP}
COPY \
    --from=0  /usr/local/bin/tini /usr/local/bin/tini

RUN set -eux; \
    groupadd --gid 1000 ${APP} && useradd --uid 1000 --gid 1000 --groups 0 --home-dir /usr/local/share/${APP} --no-create-home ${APP}

WORKDIR \
    /usr/local/share/${APP}    

USER \
    ${APP}

ENV TZ=Asia/Shanghai \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    ELASTIC_CONTAINE=true \
    ELASTIC_PRODUCT=${APP} \
    PATH=/usr/local/share/${APP}/bin:$PATH

HEALTHCHECK \
    --interval=10s --timeout=5s --start-period=1m --retries=5 CMD curl -I -f --max-time 5 --unix-socket '/usr/local/share/filebeat/data/filebeat.sock' 'http:/stats/?pretty'

ENTRYPOINT \
    ["/usr/local/bin/tini", "--", "/usr/local/share/filebeat/filebeat", "-E", "http.enabled=true", "-E", "http.host=unix:///usr/local/share/filebeat/data/filebeat.sock"]

CMD ["--environment", "container"]

