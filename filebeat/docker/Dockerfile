ARG BASE=swr.cn-north-1.myhuaweicloud.com/cxwn/openanolis/anolisos:8.8

ARG VERSION=9.1.5

ARG TARGETARCH="x86_64"

ARG APP=filebeat

FROM \
    ${BASE}

ADD --chown=1000:0 [ "${APP}-${VERSION}-linux-${TARGETARCH}.tar.gz", "/usr/local/share/" ]

COPY \
    [ "tini-amd64", "/usr/local/bin/tini" ]

RUN set -eux; \
    mv /usr/local/share/${APP}-${VERSION}-linux-${TARGETARCH} /usr/local/share/${APP}; \
    mkdir /usr/share/${APP}/{data,logs}; \
    chmod -R g=u /usr/share/${APP}; \
    chmod -R g+w /usr/share/${APP}; \
    chown -R root:root /usr/share/${APP}; \
    chmod go-w /usr/share/${APP}/${APP}.yml; \
    find /usr/share/${APP} -type d -exec chmod 0750 {} \;; \
    find /usr/share/${APP} -type f -exec chmod 0640 {} \;; \
    chmod 0750 /usr/share/${APP}/${APP}; \
    chmod 0770 /usr/share/${APP}/data /usr/share/${APP}/logs; \
    chmod +x /usr/local/bin/tini

FROM \
    ${BASE}

LABEL \
    AUTHOR="CXWN"\
    VERSION="${VERSION}"\
    BUILD_DATE="2025-10-21"

COPY \
    --from=0  /usr/local/share/${APP} /usr/local/share/${APP}
COPY \
    --from=0  /usr/local/bin/tini /usr/local/bin/tini

RUN set -eux; \
    groupadd --gid 1000 ${APP} && useradd --uid 1000 --gid 1000 --groups 0 --home-dir /usr/share/${APP} --no-create-home ${APP}

WORKDIR \
    /usr/share/${APP}    

USER \
    ${APP}

ENV TZ=Asia/Shanghai \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    ELASTIC_CONTAINE=true \
    ELASTIC_PRODUCT=${APP} \
    PATH=/usr/share/${APP}/bin:$PATH

HEALTHCHECK \
    --interval=10s --timeout=5s --start-period=1m --retries=5 CMD curl -I -f --max-time 5 --unix-socket '/usr/share/${APP}/data/${APP}.sock' 'http:/stats/?pretty'

ENTRYPOINT \
    ["tinit", "--", "/usr/share/${APP}/${APP}", "-E", "http.enabled=true", "-E", "http.host=unix:///usr/share/${APP}/data/${APP}.sock"]

CMD ["--environment", "container"]

