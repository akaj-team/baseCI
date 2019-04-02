# Android Dockerfile for check commits

FROM android-env

RUN mkdir -p ~/.gradle

# Creating project directories prepared for build when running
# `docker run`
ENV PROJECT /project
RUN mkdir -p $PROJECT

ENV GRADLE_USER_HOME /.gradle
RUN mkdir -p $GRADLE_USER_HOME

ENV GRADLE_USER_CACHE /.gradle_cache
RUN mkdir -p $GRADLE_USER_CACHE

WORKDIR $PROJECT
