FROM \
    openanolis/anolisos:8.8

LABEL \
    AUTHOR="CXWN"

# https://adoptium.net/zh-CN/temurin/releases/?os=linux&arch=x64&package=jre&version=17

ADD ["OpenJDK17U-jdk_x64_linux_hotspot_17.0.16_8.tar.gz", "/usr/local/"]

ENV JAVA_HOME="/usr/local/java"

ENV PATH="${JAVA_HOME}/bin:${PATH}" \
    TZ="Asia/Shanghai" \
    LANG="zh_CN.UTF-8"

RUN set -eux; \
    cd /usr/local; \
    mv jdk-17* java; \
    dnf -y install glibc-langpack-zh; \
    rm -rf /var/cache/yum/*; \
    dnf clean all

CMD [ "java", "-version" ]