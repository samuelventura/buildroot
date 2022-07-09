https://github.com/samuelventura/buildroot/blob/brdev/readme.txt
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/nerves_defconfig
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/readme.txt
https://buildroot.org/downloads/manual/manual.html

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

sudo dd if=output/images/sdcard.img of=/dev/sdb bs=4M conv=sync status=progress

RESULTS

- eth0 gets dhcp ip
- kmscube works
- weston and wpe works
- on-screen keyboard works

TODO

- locale
- https
- weston.ini
- wpewebkit js interaction

ISSUES

- wpewebkit window wont show mouse cursor
- xkbcommon: ERROR: couldn't find a Compose file for locale "C" (mapped to "C")
- boot output goes to connected hdmi switched to the other hdmi on login
- Webkit encountered an internal error

https sites generate:
fails with https://google.com
Webkit encountered an internal error
installing BR2_PACKAGE_CA_CERTIFICATES did not solved it

- solved by installing BR2_PACKAGE_GLIB_NETWORKING

TLS/SSL support no available; install glib-networking

- cog crash from weston terminal solve by installing BR2_PACKAGE_BITSTREAM_VERA

>cog
#cog --webprocess-failure=exit
(cog:297) Cog-Core-WARNING ** <https://wpewebkit.org/> Crash! : 
The renderer process chrashed. Reloading the page may fix the intermittent failures.

- solved by changing to glib stable

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
