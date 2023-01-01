# qt5_cross_compilation_pi4b_survey
This repo is build for cross compilation tutorial with x86 linux-ubuntu to arm Pi-OS in QT application.  
The Enviroment settings details are listed below. This repo is mainly used for QT 5.15 to Pi 4 cross compilation. 


## 1. Reference
Most of the command and enviroments settings are from the tutorials below:
>1.Command and step by step video:<https://github.com/PhysicsX/QTonRaspberryPi/tree/main/QtRaspberryPi5.14.2>  
>2.Implementation details:<https://github.com/UvinduW/Cross-Compiling-Qt-for-Raspberry-Pi-4>  
>3.Chinese Tutorial:<https://www.jianshu.com/p/0b86b42eed71>  
  
  
## 2. Enviroment
### Hardware  
Host(PC) : Intel i7-9750H + 32GB RAM  
Target(Pi) : Raspberry Pi 4 Model B  
### Software  
Virtual Machine : VMWare Player 17 +50GB Disk Space + 8GB RAM (on Windows 10)  
PC-OS : Ubuntu20.04 LTS(64-bit)  
Pi-OS : Pi OS (32-bit) Release in 2022-09-22  

## 3. Prepation
Download Pi imager from : <https://www.raspberrypi.com/software/>  
VM ware : <https://www.vmware.com/tw/products/workstation-player.html>  
Ubuntu Image : <https://www.ubuntu-tw.org/modules/tinyd0/>

## 4. Rasberry Pi Command History
```
sudo vi /etc/apt/sources.list
//uncomment the line of deb-src to get main source

//for first time prepation 
sudo apt update
sudo apt full-upgrade
sudo reboot
sudo rpi-update
sudo reboot

//important lib
sudo apt-get build-dep qt5-qmake
sudo apt-get build-dep libqt5webengine-data /*optional*/
sudo apt-get install libboost-all-dev libudev-dev libinput-dev libts-dev libmtdev-dev libjpeg-dev libfontconfig1-dev 
sudo apt-get install "^libxcb.*"
sudo apt-get install libx11-xcb-dev libxcb-keysyms1 libxcb-keysyms1-dev libxcb-image0 libxcb-image0-dev libxcb-shm0 libxcb-shm0-dev libxcb-icccm4 libxcb-icccm4-dev 
//recommend
sudo apt-get install libssl-dev libdbus-1-dev libglib2.0-dev libxkbcommon-dev libegl1-mesa-dev libgbm-dev libgles2-mesa-dev mesa-common-dev
sudo apt-get install libasound2-dev libpulse-dev gstreamer1.0-omx libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev  
sudo apt-get install libvpx-dev libsrtp0-dev libsnappy-dev libnss3-dev
sudo apt-get install flex bison libxslt-dev ruby gperf libbz2-dev libcups2-dev libatkmm-1.6-dev libxi6 libxcomposite1
sudo apt-get install libfreetype6-dev libicu-dev libsqlite3-dev libxslt1-dev libavcodec-dev libavformat-dev libswscale-dev 
sudo apt-get install libgstreamer0.10-dev gstreamer-tools libraspberrypi-dev libx11-dev libglib2.0-dev 
sudo apt-get install libatspi-dev libssl-dev libxcursor-dev libxcomposite-dev libxdamage-dev libfontconfig1-dev 
sudo apt-get install libxss-dev libxtst-dev libpci-dev libcap-dev libsrtp0-dev libxrandr-dev libnss3-dev libdirectfb-dev libaudio-dev

//folder for qt5 build file
sudo mkdir /usr/local/qt5pi
sudo chown pi:pi /usr/local/qt5pi
```
## 5. Ubuntu Command History
```
//for first preparation 
sudo apt-get update
sudo apt-get upgrade
sudo bash
apt-get install gcc git bison python gperf pkg-config
apt install make
apt install libclang-dev
apt install build-essential

//folder for Pi source file and qt build enviroment
mkdir /opt/qt5pi
chown ulas:ulas /opt/qt5pi
cd /opt/qt5pi/
mkdir sysroot
mkdir sysroot/usr
mkdir sysroot/opt

//get toolchain
wget https://releases.linaro.org/components/toolchain/binaries/latest-7/arm-linux-gnueabihf/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf.tar.xz
tar xf gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf.tar.xz 
vim ~/.bashrc
// add in bottom of bashrc:
export PATH=$PATH:/opt/qt5pi/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin

//get qt release source
wget https://qt.mirror.constant.com/official_releases/qt/5.15/5.15.2/single/qt-everywhere-src-5.15.2.tar.xz
tar xf qt-everywhere-src-5.15.2.tar.xz 

//test connectation of Pi
ping *ip to pi* //ex: ping 192.168.8.134

//rsync the specific folder in Pi
//replace steven to usr_name in Pi
//replace 192.168.8.34 to own Pi address
rsync -avz --rsync-path="sudo rsync" steven@192.168.8.34:/usr/include sysroot/usr
rsync -avz --rsync-path="sudo rsync" steven@192.168.8.34:/lib sysroot
rsync -avz --rsync-path="sudo rsync" steven@192.168.8.34:/usr/lib sysroot/usr 
rsync -avz --rsync-path="sudo rsync" steven@192.168.8.34:/opt/vc sysroot/opt

//revise the relative link with files in Ubuntu
wget https://raw.githubusercontent.com/riscv/riscv-poky/master/scripts/sysroot-relativelinks.py
chmod +x sysroot-relativelinks.py 
./sysroot-relativelinks.py sysroot

//check and build qt
mkdir qt5build
cd qt5build/
../qt-everywhere-src-5.15.2/configure -opengl es2 -device linux-rasp-pi4-v3d-g++ -device-option CROSS_COMPILE=/opt/qt5pi/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf- -sysroot /opt/qt5pi/sysroot -prefix /usr/local/qt5pi -opensource -confirm-license -skip qtscript -skip qtwayland -skip qtdatavis3d -nomake examples -make libs -pkg-config -no-use-gold-linker -v
```

Make sure the part of summary of configure is same as below:
```
...
EGL .................................... yes
OpenVG ................................. no
OpenGL:
Desktop OpenGL ....................... no
OpenGL ES 2.0 ........................ yes
OpenGL ES 3.0 ........................ yes
OpenGL ES 3.1 ........................ yes
OpenGL ES 3.2 ........................ yes
Vulkan ................................. yes
Session Management ..................... yes
Features used by QPA backends:
evdev .................................. yes
libinput ............................... yes
INTEGRITY HID .......................... no
mtdev .................................. yes
tslib .................................. yes
xkbcommon .............................. yes
X11 specific:
XLib ................................. yes
XCB Xlib ............................. yes
EGL on X11 ........................... yes
QPA backends:
DirectFB ............................... no
EGLFS .................................. yes
EGLFS details:
EGLFS OpenWFD ........................ no
EGLFS i.Mx6 .......................... no
EGLFS i.Mx6 Wayland .................. no
EGLFS RCAR ........................... no
EGLFS EGLDevice ...................... yes
EGLFS GBM ............................ yes
EGLFS VSP2 ........................... no
EGLFS Mali ........................... no
EGLFS Raspberry Pi ................... no
EGLFS X11 ............................ yes
LinuxFB ................................ yes
VNC .................................... yes
XCB:
Using system-provided XCB libraries .. yes
XCB XKB .............................. yes
XCB XInput ........................... no
Native painting (experimental) ....... no
GL integrations:
GLX Plugin ......................... no
EGL-X11 Plugin ..................... yes
...
 
```
If **EGLFS Raspberry Pi** is **yes**  ,Revise the qt-everywhere-src-5-15.2/qtbase/src/gui/configure.json as the reference mention in<https://forum.qt.io/topic/132495/crosscompile-qt-5-15-2-for-raspberry-pi-4-bullseye-problems/4>  

In qt-everywhere-src-5-15.2/qtbase/src/gui/configure.json :
```
"type": "compile",
         "test": {
             "include": [ "EGL/egl.h", "bcm_host.h" ],
        this line is the original, delete it =>    "main": "vc_dispmanx_display_open(0);"
        add this lines ....
            "main": [
                "vc_dispmanx_display_open(0);",
                "EGL_DISPMANX_WINDOW_T *eglWindow = new EGL_DISPMANX_WINDOW_T;"
            ]
         },
         "use": "egl bcm_host"
     },
```
After that,do configure again. The  **EGLFS Raspberry Pi** should change to "no".  
  
If everything is right,do:
```
make -j4
make install

cd /opt/qt5pi/
//rsync the build file to Pi
rsync -avz --rsync-path="sudo rsync" sysroot/usr/local/qt5pi pi@192.168.16.25:/usr/local
```

## 6. Test in QT Creator
on Ubuntu,download QT creator by: apt install qtcreator  

In QT Creator:
1. Choose Tools -> Options
2. Select Kits(in left menu):  
        1. Choose Qt Version -> Add -> Select build qmake file(/opt/qt5pi/sysroot/usr/local/qt5pi/bin/qmake)  
        2. Choose Compilers -> Add -> GCC -> C -> Compiler path -> /opt/qt5pi/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-gcc  
        3. Choose Compilers -> Add -> GCC -> C++ -> Compiler path -> /opt/qt5pi/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-g++  
        4. Choose Debuggers -> Select System GDB at /usr/bin/gdb  
  
3.  Select Devices(in left menu):  
        1.Choose Devices->Add->Generic Linux Device->input Pi ip address and usr name->select Authentication type tp **Default**  
4.  Select Kits(in left menu):  
        1. Choose Kits -> Add -> select Device type to Generic Linux Device -> select compiler、devices、debugger、qt version add before -> Apply  

After these settings create a new project

In new porject:  
Choose Release mode and compile，the release file is ready to excute in Pi, Can use scp to pass the release file to pi.
if want to release the file directly in Pi,can follow the steps below:  

1.set the path below(**On Pi**):
```
export QT_QPA_PLATFOMRTHEME=qt5ct
export DISPLAY=:0.0
export XAUTHORITY=/home/steven/.Xauthrity
export XDG_SESSION_TYPE=x11
```
with these settings, the release file can be excuted by ssh command. To release the file to Pi directly, should change the settings in QT project.  
1.  select Projects in left menu of QT creator.
2.  select Details of Run Enviroments -> select Batch Edit and input below commands:  
```
DISPLAY=:0.0
QT_QPA_PLATFOMRTHEME=qt5ct
XAUTHORITY=/home/steven/.Xauthrity
XDG_SESSION_TYPE=x11
```
->select Fetch Device Enviroment  
3.   select Edit in left menu of QT creator->select pro file -> change target.path to the path want to put in Pi  
4.   Run the project and it should excute the project directly in Pi






