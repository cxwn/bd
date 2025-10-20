ARG BASE=swr.cn-north-1.myhuaweicloud.com/cxwn/temurin-jdk:17.0.16

FROM \
    $BASE

ARG VERSION=1.11.0

# curl -fSL -o flume.tar.gz https://dlcdn.apache.org/flume/${VERSION}/apache-flume-${VERSION}-bin.tar.gz
ADD [ "apache-flume-${VERSION}-bin.tar.gz", "/usr/local/" ]

RUN set -eux;  \
    mv /usr/local/apache-flume-${VERSION}-bin /usr/local/flume

ENV PATH=/usr/local/flume/bin:$PATH

CMD ["flume-ng", "version"]