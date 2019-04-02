# Android Dockerfile for check commits

FROM android-env

# Creating project directories prepared for build when running
# `docker run`
ENV PROJECT /project
RUN mkdir -p $PROJECT

ENV GRADLE_USER_HOME /.gradle
RUN mkdir -p $GRADLE_USER_HOME

ENV GRADLE_USER_CACHE /.gradle_cache
RUN mkdir -p $GRADLE_USER_CACHE

COPY Gemfile* /tmp/
WORKDIR /tmp
RUN bundle check || bundle install

WORKDIR $PROJECT
