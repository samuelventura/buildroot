https://github.com/samuelventura/buildroot/blob/brdev/readme.txt
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/nerves_defconfig
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/readme.txt
https://github.com/WebPlatformForEmbedded/buildroot/tree/main/configs
https://buildroot.org/downloads/manual/manual.html

https://www.raspberrypi.com/documentation/computers/config_txt.html
https://www.raspberrypi.com/documentation/computers/configuration.html#hdmi-configuration
https://avikdas.com/2018/12/31/setting-up-lcd-screen-on-raspberry-pi.html

glib stable toolchain
rpi4-b dts

kmscube
weston drm
wpewebkit
cog

make raspberrypi4_64_defconfig
make menuconfig
make savedefconfig
make -j16

#from build machine
sudo dd if=output/images/sdcard.img of=/dev/sdb bs=4M conv=sync status=progress

RESULTS

- kmscube works
- eth0 gets dhcp ip
- weston and wpe works
- on-screen keyboard works

LAUNCH

login: root
>date -s 2022-07-07
>export XDG_RUNTIME_DIR=/root
>weston
>cog

TODO

- locale
- https
- fonts
- datetime
- weston.ini
- specific screen resolution
- wpewebkit remote control
- wpewebkit js interaction
- wpe update
* show mouse cursor
* stable hdmi output
* self signed certs
* slow weston/wpe startup (rpi4b/hdmi)
* touch display output (blank)

ISSUES

- i just learned that 64bits is not stable yet
    https://www.jeffgeerling.com/blog/2022/its-official-raspberry-pi-os-goes-64-bit
- (cog:289): GLib-GIO-WARNING **: Your application does not implement g_application_activate()
    and has no handlers connected to 'activate' signal. It should do one of these.
- wpewebkit window wont show mouse cursor
    https://github.com/WebPlatformForEmbedded/meta-wpe/issues/170
    https://github.com/WebPlatformForEmbedded/meta-wpe/issues/140
    export WPE_BCMRPI_TOUCH=1
    export WPE_BCMRPI_CURSOR=1
- xkbcommon: ERROR: couldn't find a Compose file for locale "C" (mapped to "C")
    Still shows after update to en_US.UTF_8
    Needs clean make?
- TLS certificate has unknown CA, identity mismatch.
- boot output goes to connected hdmi switched to the other hdmi on login
    https://github.com/raspberrypi/firmware/issues/1647
    https://raspberrypi.stackexchange.com/questions/135025/no-hdmi-video-after-boot-sequence

- Webkit encountered an internal error

http works, https sites fail (https://google.com)
Webkit encountered an internal error
installing BR2_PACKAGE_CA_CERTIFICATES did not solved it
installing BR2_PACKAGE_LIBSOUP_SSL did not solved it
installing BR2_PACKAGE_SHARED_MIME_INFO did not solved it
installing BR2_PACKAGE_RNG_TOOLS did not solved it
full clean make new error: TLS certificate has activation time in the future
works after: date -s 2022-07-07

- TLS/SSL support no available; install glib-networking
solved by installing BR2_PACKAGE_GLIB_NETWORKING

- cog crash from weston terminal 

>cog
#cog --webprocess-failure=exit
(cog:297) Cog-Core-WARNING ** <https://wpewebkit.org/> Crash! : 
The renderer process chrashed. Reloading the page may fix the intermittent failures.
solve by installing BR2_PACKAGE_BITSTREAM_VERA

- weston fails to launch

solved by changing to glib stable
>export XDG_RUNTIME_DIR=/root
#openvt -v -s -- weston
>weston

Weston 10.0.1
Loading module '/usr/lib/libweston-10/gl-renderer.so'
Failed to load module: Error relocating /usr/lib/libEGL.so.1:  : initial-exec TLS resolvers to dynamic definition in /usr/lib/libEGL.so.1
failed to initialize libEGL
fatal: failed to create compositor backend

The raspberrypi4 does not use VC4, but VC6.

 BR2_PACKAGE_MESA3D_GALLIUM_DRIVER_V3D:                                                                                │  
  │                                                                                                                       │  
  │ Driver for Broadcom VC6 (rpi4) GPUs (needs vc4).
