# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM openjdk:8-jre-alpine

LABEL maintainer="hanahmily@apache.org"

# Install required packages
RUN apk add --no-cache \
    bash \
    python \
    su-exec


ENV SKYWALKING_HOME=/skywalking
RUN mkdir -p "${SKYWALKING_HOME}"
WORKDIR $SKYWALKING_HOME

ENV GPG_KEYS B0801BC746F965029A1338072EF5026E70A55777 D360AB2AB20B28403270E2CBE8608938DB25E06B
ENV SKYWALKING_VERSION=6.0.0-alpha
ENV SKYWALKING_SHA512 acfae5d3ef91ba7ffbb2ad7aaf58f92ea494c5c55593a2e2ad393140194cd9db7f5a5a0e57066160d10c3e556749186b4549768484a9d341d49a1af083e240ed

ENV SKYWALKING_TGZ_URLS \
        https://www.apache.org/dyn/closer.cgi?action=download&filename=incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz \
        # if the version is outdated, we might have to pull from the dist/archive :/
	    https://www-us.apache.org/dist/incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz \
	    https://www.apache.org/dist/incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz \
	    https://archive.apache.org/dist/incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz

ENV SKYWALKING_ASC_URLS \
        https://www.apache.org/dyn/closer.cgi?action=download&filename=incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz.asc \
        # if the version is outdated, we might have to pull from the dist/archive :/
	    https://www-us.apache.org/dist/incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz.asc \
	    https://www.apache.org/dist/incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz.asc \
	    https://archive.apache.org/dist/incubator/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-incubating-$SKYWALKING_VERSION.tar.gz.asc

RUN set -eux; \
	\
	apk add --no-cache --virtual .fetch-deps \
		gnupg \
		\
		ca-certificates \
		openssl \
	; \
	\
	export GNUPGHOME="$(mktemp -d)"; \
	for key in $GPG_KEYS; do \
		gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
	done; \
	\
	success=; \
	for url in $SKYWALKING_TGZ_URLS; do \
		if wget -O skywalking.tar.gz "$url"; then \
			success=1; \
			break; \
		fi; \
	done; \
	[ -n "$success" ]; \
	\
	echo "$SKYWALKING_SHA512 *skywalking.tar.gz" | sha512sum -c -; \
	\
	success=; \
	for url in $SKYWALKING_ASC_URLS; do \
		if wget -O skywalking.tar.gz.asc "$url"; then \
			success=1; \
			break; \
		fi; \
	done; \
	[ -n "$success" ]; \
	\
	gpg --batch --verify skywalking.tar.gz.asc skywalking.tar.gz; \
	tar -xvf skywalking.tar.gz --strip-components=1; \
	rm -rf bin/; \
	rm skywalking.tar.gz*; \
	command -v gpgconf && gpgconf --kill all || :; \
	rm -rf "$GNUPGHOME"; \
    apk del .fetch-deps
