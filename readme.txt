https://github.com/samuelventura/buildroot/blob/brdev/readme.txt
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/nerves_defconfig
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/readme.txt
https://github.com/WebPlatformForEmbedded/buildroot/tree/main/configs
https://buildroot.org/downloads/manual/manual.html
http://underpop.online.fr/b/buildroot/en/rebuild-pkg.htm.gz

https://wpewebkit.org/code/
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
sudo eject /dev/sdb

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

#ssh requires to set a password from rpi console
#BR2_TARGET_GENERIC_ROOT_PASSWD="rpi"
scp weston.ini root@10.77.3.171:
ssh root@10.77.3.171
date -s 2022-07-07
export XDG_RUNTIME_DIR=/root
weston --tty=1 &
weston --tty=1 -c/root/weston.ini &
export WAYLAND_DISPLAY=wayland-1
cog --webprocess-failure=exit &
cog --webprocess-failure=exit --platform=drm &

DEBUG

WPEBACKEND_FDO_FORCE_SOFTWARE_RENDERING=1 G_MESSAGES_DEBUG=all MESA_DEBUG=1 EGL_LOG_LEVEL=debug LIBGL_DEBUG=verbose WAYLAND_DEBUG=1
export WPEBACKEND_FDO_FORCE_SOFTWARE_RENDERING=1 
export G_MESSAGES_DEBUG=all 
export MESA_DEBUG=1 
export EGL_LOG_LEVEL=debug 
export LIBGL_DEBUG=verbose 
export WAYLAND_DEBUG=1

WPEBACKEND_FDO_FORCE_SOFTWARE_RENDERING=1 G_MESSAGES_DEBUG=all \
    MESA_DEBUG=1 EGL_LOG_LEVEL=debug LIBGL_DEBUG=verbose WAYLAND_DEBUG=1 \
    WAYLAND_DISPLAY=wayland-1 XDG_RUNTIME_DIR=/root \
    cog --webprocess-failure=exit > /root/log.txt 2>&1

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
- user land tools: modetest
* show mouse cursor
* stable hdmi output
* self signed certs
* slow weston/wpe startup (rpi4b/hdmi)
* touch display output (blank)
* youtube demo crashes
* feels slow even down to 1080p
* run without weston

ISSUES

- i just learned that 64bits is not stable yet
    https://www.jeffgeerling.com/blog/2022/its-official-raspberry-pi-os-goes-64-bit
- (cog:289): GLib-GIO-WARNING **: Your application does not implement g_application_activate()
    and has no handlers connected to 'activate' signal. It should do one of these.
- wpewebkit window wont show mouse cursor
    capturing the poiter by dragging from outside the window shows the cursor while passing over
    https://github.com/WebPlatformForEmbedded/meta-wpe/issues/170
    https://github.com/WebPlatformForEmbedded/meta-wpe/issues/140
    export WPE_BCMRPI_TOUCH=1
    export WPE_BCMRPI_CURSOR=1
    COG_USE_WAYLAND_CURSOR
    WaylandCursor
- xkbcommon: ERROR: couldn't find a Compose file for locale "C" (mapped to "C")
    Still shows after update to en_US.UTF_8
    Needs clean make?
- TLS certificate has unknown CA, identity mismatch.
- weston selects the highest monitor resolution
    4k in my case
    cat /sys/class/drm/card1-HDMI-A-1/modes
- cog wont go fullscreen
    export COG_PLATFORM_FDO_VIEW_FULLSCREEN=1
    export WPE_INIT_VIEW_WIDTH=1920
    export WPE_INIT_VIEW_HEIGHT=1080
- hdmi output goes blank after boot sequence
    repowering the monitor redraws the login prompt
        dmesg shows: [00:00:29.677] Output 'HDMI-A-1' enabled with head(s) HDMI-A-1
    sometimes reconnecting the hdmi cable works as well
    sometime changing the hdmi port works as well
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
