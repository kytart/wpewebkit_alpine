# balena image wraps QEMU that simulates Raspberry Pi
FROM balenalib/rpi-alpine:3.10-run
COPY repositories /etc/apk/repositories
RUN apk update && apk add alpine-sdk nano
RUN adduser root abuild
RUN abuild-keygen -a -i -n
