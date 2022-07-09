https://github.com/samuelventura/buildroot/blob/brdev/readme.txt
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/nerves_defconfig
https://github.com/samuelventura/nerves_system_x86_64/blob/wpekiosk/readme.txt
https://buildroot.org/downloads/manual/manual.html

musl toolchain
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
- weston fails with: 

>export XDG_RUNTIME_DIR=/root
>openvt -v -s -- weston

Weston 10.0.1
Loading module '/usr/lib/libweston-10/gl-renderer.so'
Failed to load module: Error relocating /usr/lib/libEGL.so.1:  : initial-exec TLS resolvers to dynamic definition in /usr/lib/libEGL.so.1
failed to initialize libEGL
fatal: failed to create compositor backend

The raspberrypi4 does not use VC4, but VC6.

 BR2_PACKAGE_MESA3D_GALLIUM_DRIVER_V3D:                                                                                │  
  │                                                                                                                       │  
  │ Driver for Broadcom VC6 (rpi4) GPUs (needs vc4).
