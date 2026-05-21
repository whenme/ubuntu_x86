# ubuntu image auto generation script

## Key features

### 1. Support multi types of ubuntu image generation/configuration
1) Install Ubuntu image from iso installation package
2) Install Ubuntu image from pre-installed image
3) install/run image(img/hdd image) in qemu to customerize the image
4) With chroot to customerize image in host

### 2. Configure ubuntu image in installation and customerize image in script
1) Add configuration in user-data(in section packages). It will install the package in ubuntu installation.
2) Add configuration in param.json (in server/package). It will install the package after ubuntu installation.
3) Add customerized tools. Such as it can auto install r8125 driver (tool/r8125).
4) Add customerized configuation in script.

## Sample guides to customerized package

### 1. add customerized package in ubuntu installation
add customerized package in user-data file "packages". It will install the package in ubuntu installation. It is preferable solution to customerize image installation.

### 2. add customerized package after ubuntu installation
add customerized package in param.json "package". It will install the package in host with chroot after ubuntu installation.

## Sample command guides
### 1. help for command guids:
    ./build-image.sh -h

### 2. build image from ubuntu iso image:
    ./build-image.sh -f ubuntu-24.04.4-live-server-amd64.iso -b
  download ubuntu-24.04.4-live-server-amd64.iso to current path

### 3. build image from ubuntu preinstalled image:
    ./build-image.sh -f ubuntu-24.04.4.xz -b

### 4. run the built image with qemu
    ./build-image.sh -f amd-ubuntu.img -u
  It will run the image with qemu. It has network support in qemu. Then it can customerize ubuntu configuration and install/uninstall packages in qemu.

### 5. rootfs update
  mount image to host:

    ./build-image.sh -f amd-ubuntu.img -r 1
  It will mount the image's rootfs to temp/rootfs. At the same time, it mount host's running device file (/dev, /proc) to target image (temp/rootfs)
  It can copy host's files to temp/rootfs. Then the copied files will be put into the image.
  With image file(-f), it will copy image file to generated image. Such as above command and hostname=diag-srdc-ubuntu, it will copy amd-ubuntu.img to diag-srdc-ubuntu.img. All later operations are in diag-srdc-ubuntu.img

  With chroot, it can customerize image configuation in host. Such as it can install kernel/package the same as qemu or real image.

  command to enter chroot:    __chroot temp/rootfs__

  Then later command will be run in the target image, not in host. It can customerize package and configuation to the target image.

  command to exit chroot:     __exit__

  After customerized the image in host, it should umount host file system in target image:

    ./build-image.sh -r 0

## Some tips
1. As it needs to run in qemu, please run it in ubuntu desktop. As it need to operate virtual file system, please run it with root
2. As network support, please build image with wired network, while wireless network in building image has some fatal issue.
3. The ubuntu image's user is "ubuntu" and "root". default password is "123"
4. The image size can be changed in param.json "disk_size". The size unit is "MB"
5. The generated image file name will the same as host name. Such as host name in param.json, the generated image file will be amd-ubuntu.img
6. Preinstalled image is not recommanded as it is not offical released version. Just for testing.
7. As the image should support/run in qemu, the installed image's disk will have 3 partitions: bios_grub, boot/efi and rootfs. The first partition bios_grub(1MB) is for installation and run image in qemu.
