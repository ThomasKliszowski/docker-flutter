FROM adoptopenjdk/openjdk8:alpine

USER root

ENV LD_LIBRARY_PATH "/lib:/usr/lib"

# ----------
# Android
# ----------
ENV SDK_TOOLS "6858069"
ENV BUILD_TOOLS "30.0.2"
ENV TARGET_SDK "30"
ENV ANDROID_HOME "/opt/sdk"
ENV PATH "${PATH}:${ANDROID_HOME}/cmdline-tools/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/build-tools/${BUILD_TOOLS}"

# Install required dependencies
RUN apk add --no-cache bash git unzip wget && \
  apk add --virtual .rundeps $runDeps && \
  rm -rf /tmp/* && \
  rm -rf /var/cache/apk/*

# Download and extract Android Tools
RUN wget -q "https://dl.google.com/android/repository/commandlinetools-linux-${SDK_TOOLS}_latest.zip" -O /tmp/tools.zip && \
  mkdir -p ${ANDROID_HOME} && \
  unzip -qq /tmp/tools.zip -d ${ANDROID_HOME} && \
  rm -v /tmp/tools.zip

# Install SDK Packages
RUN mkdir -p ~/.android/ && touch ~/.android/repositories.cfg && \
  yes | sdkmanager --sdk_root=${ANDROID_HOME} --licenses && \
  sdkmanager --sdk_root=${ANDROID_HOME} "platform-tools" "extras;android;m2repository" "extras;google;m2repository" "extras;google;instantapps" && \    
  sdkmanager --sdk_root=${ANDROID_HOME} --update

# Install SDK Packages
RUN sdkmanager --sdk_root=${ANDROID_HOME} "build-tools;${BUILD_TOOLS}" "platforms;android-${TARGET_SDK}"

# ----------
# Flutter
# ----------
ARG flutter_version="stable"
ARG glibc_version="2.32-r0"

ENV FLUTTER_HOME "${HOME}/sdks/flutter"
ENV FLUTTER_VERSION "${flutter_version}"
ENV FLUTTER_ROOT "${FLUTTER_HOME}"
ENV PATH "${PATH}:${FLUTTER_HOME}/bin:${FLUTTER_HOME}/bin/cache/dart-sdk/bin"

# Install glibc, as Flutter & Dart do not support musl libc
RUN apk add --no-cache git bash file curl glu unzip zip wget openssh
RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub && \
  wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${glibc_version}/glibc-${glibc_version}.apk && \
  apk add --no-cache glibc-${glibc_version}.apk && \
  rm glibc-${glibc_version}.apk

# Install Flutter
RUN mkdir -p ${FLUTTER_HOME} && \
  git clone --branch ${FLUTTER_VERSION} --depth 1 https://github.com/flutter/flutter.git ${FLUTTER_HOME}

# Configure Flutter
RUN flutter config --no-analytics && \
  flutter precache --no-ios && \
  chown -R root:root ${FLUTTER_HOME}

# ----------
# Fastlane
# ----------
ARG fastlane_version="2.165.0"

ENV FASTLANE_VERSION "${fastlane_version}"

RUN apk add --update --no-cache \
  ca-certificates \
  wget \
  unzip \
  libstdc++ \
  g++ \
  make \
  ruby \
  ruby-webrick \
  ruby-dev \
  && rm -rf /var/cache/apk/*

# Install Fastlane
RUN gem install fastlane -N -v $FASTLANE_VERSION
