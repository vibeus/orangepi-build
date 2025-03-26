Please read documentation about customizing build configuration  
https:/www.orangepi.org

# Vibe Custom Image

## OS Image Enviroment Specs(example):
- OS: Linux Ubuntu Jammy 
- Kernel: 5.16.17
- Python: 3.10


```text
PRETTY_NAME="Orange Pi 3.0.8 Jammy"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.2 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
```
---
## Source Files and Build Scripts
### Rootfs custom build injection script
`orangepi-build/userpatches/customize-image.sh`
### Overlay files located at
`orangepi-build/userpatches/overlay`

---
## How to build custom image?
more build flags, https://docs.armbian.com/Developer-Guide_Build-Options/
. Note! Not all flags are supported.

``` shell
#prepare a ubuntu OS, get its version
VERSION=`lsb_release -a | grep Codename | awk '{ print $2 }'`
# run in sdk root path, or git has protection for folder and can't execute git operation 
su root
# run in an enviroment, which python version is python3.8. Or scripts might run abnormally 
# we can either choose alternatives python
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 1
sudo update-alternatives --set python /usr/bin/python3.8
#or create a vitual environment with venv or conda (recommand)
sudo apt update
sudo apt install python3.8 python3.8-venv python3.8-dev
python3.8 -m venv ~/orangepi-env
source ~/orangepi-env/bin/activate

#TODO(implement under command as a kernel patch)
#Notice: need to check if dts under folder "kernel/orange-pi-x.xx-sunxi64/arch/arm64/boot/dts/allwinner/#sun50i-h616-orangepi-zero2.dts has setting
&uart5 {
	pinctrl-names = "default";
	pinctrl-0 = <&uart5_ph_pins>;
	status = "okay";
};
#if it's "status = "disable", need to change as "okay" and commit it. Then
#recompile the kernel
sudo ./build.sh BOARD=orangepizero2 BUILD_OPT=kernel RELEASE=noble
 BUILD_MINIMAL=no BUILD_DESKTOP=no KERNEL_CONFIGURE=no


# build command
sudo ./build.sh BOARD=orangepizero2 BUILD_OPT=image RELEASE=$VERSION
 BUILD_MINIMAL=no BUILD_DESKTOP=no KERNEL_CONFIGURE=no
```


## Image Path 
```
#example
../orangepi-build/output/images/Orangepizero2_3.0.8_ubuntu_jammy_server_linux5.16.17/*.img
```