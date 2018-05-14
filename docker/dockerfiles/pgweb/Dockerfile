FROM alpine:3.7
WORKDIR /
RUN  apk update \
  && apk add --no-cache unzip \
  && rm -rf /var/cache/apk/* \
  && wget https://github.com/sosedoff/pgweb/releases/download/v0.9.12/pgweb_linux_amd64.zip \
  && unzip pgweb_linux_amd64.zip \
  && rm -f pgweb_linux_amd64.zip \
  && mkdir -p /opt/pgweb \
  && cp /pgweb_linux_amd64 /opt/pgweb \
  && rm /pgweb_linux_amd64
EXPOSE 8080
VOLUME /opt/pgweb
CMD ["/opt/pgweb/pgweb_linux_amd64", "--sessions", "--bind=0.0.0.0", "--listen=8080"]