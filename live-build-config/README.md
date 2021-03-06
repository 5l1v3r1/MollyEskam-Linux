## Live Build a Custom Kali ISO
### An Introduction to Building Your Own Kali ISO

## Building a customized Kali ISO is easy, fun, and rewarding. You can configure virtually any aspect of your Kali ISO build using the Debian live-build scripts. These scripts allow developers to easily build live system images by providing a framework that uses a configuration set to automate and customize all aspects of building the image. The Kali Linux development team has adopted these scripts and they’re used to produce the official Kali ISO releases.
## Where Should You Build Your ISO?

## Ideally, you should build your custom Kali ISO from within a pre-existing Kali environment.
## Getting Ready — Setting up the live-build system

## We first need to prepare the Kali ISO build environment by installing and setting up live-build and its requirements with the following commands:

##### sudo apt update
##### sudo apt install -y curl git live-build cdebootstrap
##### git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git

## Now you can simply build an updated Kali ISO by entering the “live-build-config” directory and running our build.sh wrapper script, as follows:

##### cd live-build-config/
##### ./build.sh --verbose

## The build.sh script will take a while to complete, as it downloads all of the required packages needed to create your ISO. Good time for a coffee.
## Configuring the Kali ISO Build (Optional)

## If you want to customize your Kali Linux ISO, this section will explain some of the details. Through the kali-config directory, the Kali Linux live build supports a wide range of customization options, which are well-documented on the Debian live build 4.x page. However, for the impatient, here are some of the highlights.
## Building Kali with Different Desktop Environments

## Since Kali 2.0, we now support built in configurations for various desktop environments, including KDE, Gnome, E17, I3WM, LXDE, MATE and XFCE. To build any of these, you would use syntax similar to the following:

##### # These are the different Desktop Environment build options:
##### #./build.sh --variant {gnome,kde,xfce,mate,e17,lxde,i3wm} --verbose

##### # To build a KDE ISO:
##### ./build.sh --variant kde --verbose
##### # To build a MATE ISO:
##### ./build.sh --variant mate --verbose

##### #...and so on.

## Controlling the packages included in your build

## The list of packages included in your build will be present in the the respective kali-$variant directory. For example, if you’re building a default Xfce ISO, you would use the following package lists file - kali-config/variant-xfce/package-lists/kali.list.chroot. By default, this list includes the “kali-linux-default” metapackage, as well as some others. These can be commented out and replaced with a manual list of packages to include in the ISO for greater granularity.
## Build hooks, binary and chroot

## Live-build hooks allows us to hook scripts in various stages of the Kali ISO live build. For more detailed information about hooks and how to use them, refer to the live build manual. As an example, we recommend you check out the existing hooks in kali-config/common/hooks/.
## Overlaying files in your build

## You have the option to include additional files or scripts in your build by overlaying them on the existing filesystem, inside the includes.{chroot,binary,installer} directories, respectively. For example, if we wanted to include our own custom script into the /root/ directory of the ISO (this would correspond to the “chroot” stage), then we would drop this script file in the kali-config/common/includes.chroot/ directory before building the ISO.
## Building a Kali Linux ISO for older i386 architectures

## The Kali Linux i386 ISO has PAE enabled. If you require a default kernel for older hardware with PAE disabled, you will need to rebuild a Kali Linux ISO. The rebuilding process is much the same as described above, except that the 686-pae parameter that needs to be changed to 586 in auto/config as follows. First, install the prerequisites.

##### sudo apt install -y git live-build cdebootstrap debootstrap
##### git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git

## Next, make the change in auto/config for the appropriate architecture:

##### cd live-build-config/
##### sed -i 's/686-pae/686/g' auto/config

## Finally, run your build.

##### ./build.sh --arch i386 --verbose

## Building Kali on Non-Kali Debian Based Systems

## You can easily run live-build on Debian based systems other than Kali. The instructions below have been tested to work with both Debian and Ubuntu.

## First, we prep the system by ensuring it is fully updated, then proceed to download the Kali archive keyring and live-build packages.

##### sudo apt update
##### sudo apt upgrade
##### cd /root/

##### wget http://http.kali.org/pool/main/k/kali-archive-keyring/kali-archive-keyring_2018.2_all.deb
##### wget https://archive.kali.org/kali/pool/main/l/live-build/live-build_20190311_all.deb

## With that completed, we install some additional dependencies and the previously downloaded files.

##### sudo apt install -y git live-build cdebootstrap debootstrap curl
##### sudo dpkg -i kali-archive-keyring_2018.2_all.deb
##### sudo dpkg -i live-build_20190311_all.deb

## With the environment all prepared, we start the live-build process by setting up the build script and checking out the build config.

##### cd /usr/share/debootstrap/scripts/
##### echo "default_mirror http://http.kali.org/kali"; sed -e "s/debian-archive-keyring.gpg/kali-archive-keyring.gpg/g" sid > /tmp/kali
##### sudo mv /tmp/kali .
##### sudo ln -s kali kali-rolling

##### cd ~
##### git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git

##### cd live-build-config/

## At this point, we have to edit the build.sh script to bypass a version check. We do this by commenting out the “exit 1” below.

##### # Check we have a good debootstrap
##### ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
##### if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
##### if ! echo "$ver_debootstrap" | grep -q kali; then
##### echo "ERROR: You need debootstrap >= 1.0.97 (or a Kali patched debootstrap). Your current version: $ver_debootstrap" >&2
##### exit 1
##### fi
##### fi

## With that change made, the script should like as follows:

##### # Check we have a good debootstrap
##### ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
##### if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
##### if ! echo "$ver_debootstrap" | grep -q kali; then
##### echo "ERROR: You need debootstrap >= 1.0.97 (or a Kali patched debootstrap). Your current version: $ver_debootstrap" >&2
# exit 1
##### fi
##### fi

## At this point, we can build our ISO as normal

##### sudo ./build.sh --variant light --verbose

### Updated on: 2020-Feb-22
### Author: g0tmi1k
