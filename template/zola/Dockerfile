FROM openfaas/of-watchdog:0.7.7 as watchdog

FROM alpine:3.11 AS build

WORKDIR /opt/site

COPY ./function .

RUN echo http://dl-cdn.alpinelinux.org/alpine/edge/testing/ >> /etc/apk/repositories \
 && apk add --update --no-cache zola \
 && zola build

FROM alpine:3.11 AS runtime

WORKDIR /opt/site

COPY --from=build    /opt/site/public public
COPY --from=watchdog /fwatchdog /usr/bin/fwatchdog

RUN chmod +x /usr/bin/fwatchdog

ENV mode="static"
ENV static_path="/opt/site/public"

HEALTHCHECK --interval=3s CMD [ -e /tmp/.lock ] || exit 1

CMD ["fwatchdog"]
