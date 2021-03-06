FROM mvertes/alpine-mongo

ADD init.sh /init.sh
ADD ./dump /

RUN chmod +x /init.sh && \
	apk update && apk upgrade && \
	apk add --no-cache build-base && \
	apk add --no-cache psmisc && \  
	rm -rf /var/cache/apk/* || return 0;

ENV GOSU_VERSION 1.10
RUN set -ex; \
	\
	apk add --no-cache --virtual .gosu-deps \
	dpkg \
	gnupg \
	openssl \
	; \
	\
	dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
	wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
	wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
	\
	# verify the signature
	export GNUPGHOME="$(mktemp -d)"; \
	export GPG_KEYS="B42F6819007F00F88E364FD4036A9C25BF357DD4"; \
	( gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEYS" \
	|| gpg --keyserver pgp.mit.edu --recv-keys "$GPG_KEYS" \
	|| gpg --keyserver keyserver.pgp.com --recv-keys "$GPG_KEYS" ); \
	gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
	rm -rf "$GNUPGHOME" || return 0; \
	rm -rf /usr/local/bin/gosu.asc || return 0; \
	\
	chmod +x /usr/local/bin/gosu; \
	# verify that the binary works
	gosu nobody true; \
	\
	apk del .gosu-deps; \
	rm -rf /var/cache/apk/* || return 0;

ENTRYPOINT ["/init.sh"]
# CMD ["tail","-f", "/dev/null"]