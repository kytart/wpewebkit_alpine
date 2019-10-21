# How to build everything

## Run QEMU Docker container

1. If on Ubuntu, run this magic
```
mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc
echo ':arm:M::\x7fELF\x01\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x28\x00:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/qemu-arm-static:' > /proc/sys/fs/binfmt_misc/register
```

2. Build and run Docker container
```
docker build -t wpewebkit-build docker
docker run --name wpewebkit_build -it --rm wpewebkit-build sh
```

## Clone this repo

```
apk update && apk add git
git clone https://github.com/kytart/wpewebkit_alpine.git
cd wpewebkit_alpine
git checkout redo-according-to-yocto
```

## Prepare build

1. edit /etc/abuild.conf
    ```
    # vim /etc/abuild.conf
    ```
    * set number of jobs to 4 `export JOBS=4`
    * edit CXXFLAGS - remove `-Os`

1. Build and install raspberrypi
    ```
    cd raspberrypi
    abuild -F checksum
    abuild -Fr
    apk add --repository /root/packages/wpewebkit_alpine/armhf/ raspberrypi-dev
    # fix some issues with not finding rpi libs and header files
    ln -s /opt/vc/lib/libbcm_host.so /lib/libbcm_host.so
    ln -s /opt/vc/include/EGL /usr/include/EGL
    ln -s /opt/vc/include/interface /usr/include/interface
    ```

1. Build and install libepoxy-rpi
    ```
    cd libepoxy-rpi
    abuild -F checksum
    abuild -Fr
    apk add --repository /root/packages/wpewebkit_alpine/armhf/ libepoxy-rpi-dev
    ```

1. Build and install gst-plugins-base
    ```
    cd gst-plugins-base-rpi
    abuild -F checksum
    abuild -Fr
    apk add --repository /root/packages/wpewebkit_alpine/armhf/ gst-plugins-base-dev
    ```

1. Build and install libwpe
    ```
    cd libwpe
    abuild -F checksum
    abuild -Fr
    apk add --repository /root/packages/wpewebkit_alpine/armhf/ libwpe
    ```

1. Build and install wpebackend-rdk
    ```
    cd wpebackend-rdk
    abuild -F checksum
    abuild -Fr
    apk add --repository /root/packages/wpewebkit_alpine/armhf/ wpebackend-rdk
    ```

1. Build and install xdg-dbus-proxy
    ```
    cd xdg-dbus-proxy
    abuild -F checksum
    abuild -Fr
    apk add --repository /root/packages/wpewebkit_alpine/armhf/ xdg-dbus-proxy
    ```

1. Build and install wpewebkit
    ```
    cd wpewebkit
    abuild -F checksum
    abuild -Fr
    apk add --repository /root/packages/wpewebkit_alpine/armhf/ wpewebkit
    ```

1. Build cog
    ```
    cd cog
    abuild -F checksum
    abuild -Fr
    ```

1. make an archive of all the *.apk files because they have to transfered to a real Raspberry Pi running Alpine Linux so we can run it
    ```
    cd /root
    mkdir wpewebkit_apks
    cp /root/packages/wpewebkit_alpine/armhf/*.apk wpewebkit_apks
    tar -czf wpewebkit_apks.tar.gz wpewebkit_apks
    ```
