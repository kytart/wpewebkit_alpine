# balena image wraps QEMU that simulates Raspberry Pi
FROM balenalib/rpi-alpine:3.10-run
COPY repositories /etc/apk/repositories
RUN apk update && apk add alpine-sdk raspberrypi-dev nano
RUN adduser root abuild
RUN abuild-keygen -a -i -n

# fix some issues with not finding rpi libs and header files
RUN ln -s /opt/vc/lib/libbcm_host.so /lib/libbcm_host.so
RUN ln -s /opt/vc/include/EGL /usr/include/EGL
RUN ln -s /opt/vc/include/interface /usr/include/interface
