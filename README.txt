[SEQUENCE TO BUILD BALENA CONTAINER:]
--------------------------------------------------------------------------------
1. dl github repo
https://github.com/balena-io-library/base-images

2. cd base-images/balena-base-images/armv7hf/alpine/3.10

3. run this magic

login as root:
--------------------
mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc
echo ':arm:M::\x7fELF\x01\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x28\x00:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/qemu-arm-static:' > /proc/sys/fs/binfmt_misc/register

4. docker build
sudo docker build ./build/

5. run docker container.
sudo docker run -it 82f4029375ba  sh

(raspberry:)
sudo docker run -it 0355bee82d1c  sh

(cat /etc/apk/arch	- shows arch for running docker image)

[DL git with APKBUILD files on alpine machine:]
--------------------------------------------------------------------------------
git clone https://github.com/repu1sion/wpewebkit_alpine.git


[Create Alpine package:]
--------------------------------------------------------------------------------
0. install all needed packages
# apk add vim cmake ninja mesa-dev libxkbcommon-dev wayland-dev ruby cairo-dev icu-dev libepoxy-dev libgcrypt-dev libsoup-dev libwebp-dev atk-dev harfbuzz-dev at-spi2-atk-dev openjpeg-dev openjpeg-tools woff2-dev libtasn1-dev libxslt-dev bubblewrap libseccomp-dev gstreamer-dev gst-plugins-base-dev gst-plugins-bad-dev gperf autoconf autoconf-archive automake wayland-protocols

1. Install alpine sdk.
# apk add alpine-sdk

2. Create user and add to /etc/sudoers
# adduser <yourusername>

2.1 Append line
# sudo vim /etc/sudoers
<yourusername>    ALL=(ALL) ALL

3. logout as root and login as new user
# login

4. uncomment second line in /etc/apk/repositories (community repo).
Then update list of packets.
# apk update

5. edit /etc/abuild.conf
# sudo vim /etc/abuild.conf
  export JOBS=4

  edit CXXFLAGS - remove -Os

6. To use 'abuild -r' command to install dependency packages automatically.
# sudo addgroup <yourusername> abuild

7. logout and login again for the group change to have effect
# (su - root)   - relogin

8. We also need to prepare the location where the build process caches files
  when they are downloaded. By default this is /var/cache/distfiles/.
# sudo mkdir -p /var/cache/distfiles
# sudo chmod a+w /var/cache/distfiles

9. cd to dir with APKBUILD. Sequence to build: libwpe, wpebackend-fdo, xdg-dbus-proxy, wpewebkit.

10. generate security keys
# abuild-keygen -a -i
(key will be created in /etc/apk/keys)

11. make symlinks
# ln -s /opt/vc/include/EGL /usr/lib/EGL
# ln -s /opt/vc/include/interface /usr/lib/interface

12. checksum
# abuild checksum
(abuild -F checksum)
(abuild-keygen -a -i && abuild -F checksum)

13. build package itself
# abuild -r
or
# abuild -Fr

(# CHOST=armhf abuild -Fr)

14. testing package locally (not usually needed)
# sudo apk add --repository /home/USER/packages/testing $pkgname

# apk add --repository /root/packages/git/ libwpe               - really working command for wpe packet
# apk add --repository /root/packages/git/ wpebackend-fdo
# apk add --repository /root/packages/git/ xdg-dbus-proxy


# apk add --repository /root/packages/wpewebkit_alpine/ libwpe



P.S. Replacing LowLevelInterpreter flag -Os with -O1
awk '/LowLevelInterpreter.cpp.o.d/ {f=1; print; next} f {sub("-Os","-O1",$0);f=0} 1' build.ninja > 1
mv 1 build.ninja




























