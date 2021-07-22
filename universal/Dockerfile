FROM alpine:latest

USER root

ARG flutter_version="stable"
ARG glibc_version="2.33-r0"

ENV FLUTTER_HOME "${HOME}/sdks/flutter"
ENV FLUTTER_VERSION "${flutter_version}"
ENV FLUTTER_ROOT "${FLUTTER_HOME}"
ENV PATH "${PATH}:${FLUTTER_HOME}/bin:${FLUTTER_HOME}/bin/cache/dart-sdk/bin"
ENV LD_LIBRARY_PATH "/lib:/usr/lib"

# Install glibc, as Flutter & Dart do not support musl libc
RUN apk add --no-cache git bash file curl glu unzip zip wget openssh xdg-user-dirs
RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub && \
  wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${glibc_version}/glibc-${glibc_version}.apk && \
  apk add --no-cache glibc-${glibc_version}.apk && \
  rm glibc-${glibc_version}.apk

# Install Flutter
RUN mkdir -p ${FLUTTER_HOME} && \
  git clone --branch ${FLUTTER_VERSION} --depth 1 https://github.com/flutter/flutter.git ${FLUTTER_HOME}

# Configure Flutter
RUN flutter config --no-analytics && \
  flutter precache --no-android --no-ios && \
  chown -R root:root ${FLUTTER_HOME}
