/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew install qemu
export QEMU=$(which qemu-system-arm)

brew install wget
wget https://github.com/markyjackson-taulia/qemu-pi-kernel/raw/master/qemu-kernel-5.4.51-buster
export RPI_KERNEL=./kernel-qemu-4.4.34-jessie

wget http://downloads.raspberrypi.org/raspbian/images/raspbian-2016-11-29/2016-11-25-raspbian-jessie.zip
unzip 2016-11-25-raspbian-jessie.zip
export RPI_FS=./2016-11-25-raspbian-jessie.img

$QEMU -kernel $RPI_KERNEL \
-cpu arm1176 -m 256 \
-M versatilepb -no-reboot -serial stdio \
-append "root=/dev/sda2 panic=1 rootfstype=ext4 rw init=/bin/bash" \
-drive "file=$RPI_FS,index=0,media=disk,format=raw"

sed -i -e 's/^/#/' /etc/ld.so.preload

sed -i -e 's/^/#/' /etc/fstab

$QEMU -kernel $RPI_KERNEL \
-cpu arm1176 -m 256 \
-M versatilepb -no-reboot -serial stdio \
-append "root=/dev/sda2 panic=1 rootfstype=ext4 rw" \
-drive "file=$RPI_FS,index=0,media=disk,format=raw" \
-net user,hostfwd=tcp::5022-:22

ssh -p 5022 pi@localhost
