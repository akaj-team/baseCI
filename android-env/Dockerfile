# Android Dockerfile

FROM ubuntu:14.04

# Sets language to UTF8 : this works in pretty much all cases
ENV LANG en_US.UTF-8
RUN locale-gen $LANG

# Add build user account, values are set to default below
ENV RUN_USER androidci
ENV RUN_UID 5089

ENV DOCKER_ANDROID_LANG en_US
ENV DOCKER_ANDROID_DISPLAY_NAME androidci-docker

# Never ask for confirmations
ENV DEBIAN_FRONTEND noninteractive

# Update apt-get
RUN rm -rf /var/lib/apt/lists/*
RUN apt-get update
RUN apt-get dist-upgrade -y

# Installing packages
RUN apt-get install -y \
  autoconf \
  build-essential \
  bzip2 \
  curl \
  gcc \
  git \
  groff \
  lib32stdc++6 \
  lib32z1 \
  lib32z1-dev \
  lib32ncurses5 \
  lib32bz2-1.0 \
  libc6-dev \
  libgmp-dev \
  libmpc-dev \
  libmpfr-dev \
  libxslt-dev \
  libxml2-dev \
  m4 \
  make \
  ncurses-dev \
  ocaml \
  openssh-client \
  pkg-config \
  python-software-properties \
  rsync \
  software-properties-common \
  unzip \
  wget \
  zip \
  zlib1g-dev \
  --no-install-recommends

# Add ppa
RUN apt-add-repository ppa:openjdk-r/ppa
RUN apt-add-repository ppa:brightbox/ruby-ng
RUN apt-get update

# Install java
RUN apt-get -y install openjdk-8-jdk

# Install Ruby
RUN apt-get install -y ruby2.4 ruby2.4-dev
RUN gem install bundle
RUN gem install nokogiri -v 1.8.1

# Clean Up Apt-get
RUN rm -rf /var/lib/apt/lists/*
RUN apt-get clean

# Update java certificate
RUN update-ca-certificates -f

# Install Android SDK
RUN wget https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip
RUN unzip sdk-tools-linux-3859397.zip -d android-sdk
RUN mv android-sdk /usr/local/android-sdk
RUN rm sdk-tools-linux-3859397.zip

# Install Android tools
RUN yes | /usr/local/android-sdk/tools/bin/sdkmanager --licenses

RUN yes | /usr/local/android-sdk/tools/bin/sdkmanager \
"platform-tools" \
"platforms;android-26" \
"build-tools;26.0.2"

# Install Android NDK
#RUN wget http://dl.google.com/android/repository/android-ndk-r12-linux-x86_64.zip
#RUN unzip android-ndk-r12-linux-x86_64.zip
#RUN mv android-ndk-r12 /usr/local/android-ndk
#RUN rm android-ndk-r12-linux-x86_64.zip

# Environment variables
ENV ANDROID_HOME /usr/local/android-sdk
ENV ANDROID_SDK_HOME $ANDROID_HOME
ENV ANDROID_NDK_HOME /usr/local/android-ndk
ENV JENKINS_HOME $HOME
ENV PATH ${INFER_HOME}/bin:${PATH}
ENV PATH $PATH:$ANDROID_SDK_HOME/tools
ENV PATH $PATH:$ANDROID_SDK_HOME/tools/bin
ENV PATH $PATH:$ANDROID_SDK_HOME/platform-tools
ENV PATH $PATH:$ANDROID_NDK_HOME

# Export JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/

# Support Gradle
ENV TERM dumb
ENV JAVA_OPTS "-Xms2048m -Xmx3072m"
ENV GRADLE_OPTS "-XX:+UseG1GC -XX:MaxGCPauseMillis=1000"

# Cleaning
RUN apt-get clean

RUN id $RUN_USER || adduser --uid "$RUN_UID" \
    --gecos 'Build User' \
    --shell '/bin/sh' \
    --disabled-login \
    --disabled-password "$RUN_USER"

# Fix permissions
#RUN chown -R $RUN_USER:$RUN_USER $ANDROID_HOME $ANDROID_SDK_HOME
RUN chmod -R a+rx $ANDROID_HOME $ANDROID_SDK_HOME

#RUN chown -R $RUN_USER:$RUN_USER $ANDROID_NDK_HOME
#RUN chmod -R a+rx $ANDROID_NDK_HOME

#RUN echo "sdk.dir=$ANDROID_HOME" > local.properties
