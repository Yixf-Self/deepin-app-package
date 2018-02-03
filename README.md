## 深度系统原创应用打包



[![Build Status](https://travis-ci.org/qanno/deepin-screenshot.svg?branch=master)](https://travis-ci.org/qanno/deepin-screenshot)

### 方式一：Docker 自动化打包

仓库： https://github.com/qanno/deepin-screenshot

1. `chmod +x ./Deepin_Screenshot-x86_64.AppImage `

2. `./Deepin_Screenshot-x86_64.AppImage --appimage-extract`  

3. 删除 展开后其目录下的 `./usr/lib/libsystemd.so.0`

4. 删除原有的 AppRun，然后添加新的 AppRun ，其内容:

   ```bash
   #!/bin/bash
   SCRIPT_DIR=$(cd `dirname "$0"`; pwd)
   BASE_DIR=`dirname "$SCRIPT_DIR"`

   LD_LIBRARY_PATH="${SCRIPT_DIR}/usr/lib:$LD_LIBRARY_PATH"
   if [ ! -z "$APPIMAGE_WORKDIR" ]; then
   	if ! cd "$APPIMAGE_WORKDIR"; then
   		echo "Cannot change directory to \"$APPIMAGE_WORKDIR\" (APPIMAGE_WORKDIR)"
   		exit 1
   	fi
   fi
   if [ "$1" = "--appimage-exec" ]; then
   	if ! "${@:2}"; then
   		exit 1
   	fi
   else
   	if !  env DISPLAY=$DISPLAY XAUTHORITY=$XAUTHORITY LD_LIBRARY_PATH=$LD_LIBRARY_PATH "$SCRIPT_DIR/usr/bin/deepin-screenshot" "--icon"; then
   		exit 1
   	fi
   fi
   ```

   最后重新用 appimagetool 打包。

### 方式二： 手动打包

[深度系统](https://www.deepin.org)有很多的原创的优秀应用，比如：深度截图、深度录屏、深度录音、深度音乐、深度日历等，可惜它们默认只能在深度的桌面环境下运行。对于上述应用可以采用此 [PPA](https://launchpad.net/~leaeasy/+archive/ubuntu/dde) 来安装(**需要 Ubuntu 17.04+**)：

```
sudo add-apt-repository ppa:leaeasy/dde
sudo apt-get update
sudo apt-get install dde
sudo apt-get install deepin-screen-recorder
...
```

如果你愿意安装深度的桌面环境且 Ubuntu 17.04+ 的话，可以考虑上面的 PPA 的办法。

下面进入正题(二进制文件架构均为 amd64)，从上面的 PPA 下载相应的 deb 包，以 [`deepin-screenshot`](https://launchpad.net/~leaeasy/+archive/ubuntu/dde/+files/deepin-screenshot_4.0.10-2+ubuntu17.10_amd64.deb) 举例，下载好后，解压出二进制文件，   `ldd ./deepin-screenshot` 查看依赖：

```
libQt5Widgets.so.5: version `Qt_5' 
libQt5Core.so.5: version `Qt_5.9' 
libQt5Core.so.5: version `Qt_5' 
libQt5Gui.so.5: version `Qt_5' 
libQt5DBus.so.5: version `Qt_5' 
linux-vdso.so.1 
libdtkwidget.so.2
libdtkcore.so.2 
libdtkwm.so.2 
libQt5Widgets.so.5 
libQt5Gui.so.5 
libQt5DBus.so.5 
libQt5Core.so.5 
libstdc++.so.6 
libm.so.6
libgcc_s.so.1 
libc.so.6 
libpthread.so.0
libgobject-2.0.so.0 
libglib-2.0.so.0 
libX11.so.6 
libpng12.so.0 
libharfbuzz.so.0 
libz.so.1 
libGL.so.1 
libdbus-1.so.3 
libicui18n.so.55 
libicuuc.so.55 
libpcre16.so.3
libdl.so.2
librt.so.1 
ld-linux-x86-64.so.2
libffi.so.6 
libpcre.so.3 
libxcb.so.1 
libfreetype.so.6 
libgraphite2.so.3 
libnvidia-tls.so.340.102 
libnvidia-glcore.so.340.102 
libXext.so.6 
libsystemd.so.0 
libicudata.so.55 
libXau.so.6 
libXdmcp.so.6 
libselinux.so.1 
liblzma.so.5 
libgcrypt.so.20 
libgpg-error.so.0 
```

​    以上依赖非必须，视不同发行版而定，但可按此法查看所缺依赖，在 [Pkgs](https://pkgs.org) 和 [Rpmfind](https://www.rpmfind.net) 中找到并下载相应的包库。

这里打包的 AppImage 与 Portable 均在 Linux Mint 18.2(基于 Ubuntu 16.04) 测试可用。



暂时只给出 深度截图 的 AppImage 与 Portable 。(目前也比较粗糙)