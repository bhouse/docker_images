FROM bhouse/alpine-ruby:2.2.2
MAINTAINER ben@benhouse.io

# Use libxml2 and libxslt packages from alpine for building nokogiri
ENV NOKOGIRI_USE_SYSTEM_LIBRARIES=true

# Don't install any docs for ruby gems
RUN echo 'gem: --no-rdoc --no-ri' > /etc/gemrc

# Install packages required for building and installing gems for rails
# build-dependencies is a virtual package to uninstall packages not required at
# runtime
RUN apk-install --virtual build-dependencies build-base zlib-dev libxml2-dev sqlite-dev

# Packages required for rails
RUN apk-install libxslt-dev libxml2-dev nodejs mysql-client postgresql-client sqlite ruby-dev

# Set timezone to UTC
RUN apk-install tzdata && \
  cp /usr/share/zoneinfo/UTC /etc/localtime && \
  echo 'UTC' > /etc/timezone && \
  apk del tzdata

# throw errors if Gemfile has been modified since Gemfile.lock
RUN bundle config --global frozen 1

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

ONBUILD COPY Gemfile /usr/src/app/
ONBUILD COPY Gemfile.lock /usr/src/app/
ONBUILD RUN bundle install

ONBUILD COPY . /usr/src/app

# Remove build packages to reduce image size
ONBUILD RUN apk del build-dependencies

EXPOSE 3000
CMD ["rails", "server", "-b", "0.0.0.0"]
