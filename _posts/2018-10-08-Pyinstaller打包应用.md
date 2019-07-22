---
layout: post
title: Pyinstaller instructions
categories:
  - System Package
description: Pyinstaller instructions
keywords: 'pyinstaller, dmg, pyqt5'
comments: true
---

# Pyinstaller instructions
[Pyinstaller官网](https://pyinstaller.readthedocs.io/en/v3.3.1/installation.html\)

## 一、安装

###### 使用pip安装

```
pip install pyinstaller
```

> 更新 pip install --upgrade pyinstaller
>
> 安装开发版 pip install [https://github.com/pyinstaller/pyinstaller/tarball/develop](https://github.com/pyinstaller/pyinstaller/tarball/develop)

##### 打包一

```
pyinstaller xxx.py -w --windowed --hidden-import xxx
```

> 打包包含.app，同时隐藏某些包

##### 打包二

```
pyinstaller xxx.py -w -i xxx/xxx.icns
```

## 二、打包错误

1，打包后复制resource 到打包的运行根目录，同时复制粘贴jedi

2，报错AttributeError: module 'serial' has no attribute 'Serial'

```
# 解决办法卸载pyserial与serial 重新安装pyserial
```

## 三、常见错误

1，Qt平台缺少插件

```
qt.qpa.plugin: Could not find the Qt platform plugin "cocoa" in "/Users/xxx/diicroduino.app/Contents/MacOS/PyQt5/plugins/platforms"
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.
```

> 可能虚拟环境的原因，建议用原生环境再次尝试

2，缺少PyQt5.sip

```
ModuleNotFoundError: No module named 'PyQt5.sip'
```

> 打包时忽略PyQt5.sip : Pyinstaller xxx.py -w windowed --hidden-import PyQt5.sip

3，缺少静态文件

```
FileNotFoundError: [Errno 2] No such file or directory: '/Users/xxx.app/Contents/MacOS/resources/css/night.css'
```

> 将项目中的静态文件复制到MacOS下，例如本项目中的resource静态文件目录整体复制到MacOS目录下

4， 缺少fake文件

```
FileNotFoundError: [Errno 2] No such file or directory: '/Users/xxx.app/Contents/MacOS/jedi/evaluate/compiled/fake'
[7089] Failed to execute script xxx
```

> 将jedi复制j到MacOS目录下，[点击下载Jedi](/Other/jedi.zip)

## 四、安装Qt

1.安装Qt

```
brew install qt
```

2.配置Qt环境变量

```
echo 'export PATH="/usr/local/opt/qt/bin:$PATH"'>> ~/.bash_profile

source ~/.bash_profile
```

3.查看Qt版本

```
qmake -v
```

## 五、Python跨平台打包

对于pyinstaller，可以完成在windows,linux,和mac下的python脚本编译，生成exe,elf,.app文件；

1，使用方法；

在pyinstaller的官网上下载，一般是源码包，可以解压缩之后直接使用：

python pyinstaller.py  test.py

在pyinstaller的目录下 生成一个test目录，并且含有buid和dist两个子目录，子目录dist中存放可执行文件；

2，如果需要编译成单个文件：

```
python pyinstaller.py   -F  test.py
```

> 生成单个的可执行文件

3，如果需要添加软件的图标：

```
python pyinstaller.py   -F  test.py   -i test.icon
```

> 注意一定是icon文件才行；

4，如果编译GUI项目，可以使用

```
python  pyinstaller.py -w test.py
```

或者：

```
python  pyinstaller.py -w test.pyw
```

或者：

```
python  pyinstaller.py -w --noconsole（ windwos平台下）
```

> linux下默认没有弹窗（弹出terminal）；

5，在mac环境下编译，含有app可执行文件.

```
python  pyinstaller.py  test.py -w  --windowed
```

> 此时在项目目录下含有unix可执行文件和app文件

6，为应用添加指定图标

```
pyinstaller -w -i xxx.ico(或xxx.icns) xxx.py
```

7，Mac平台下打包dmg格式

1&gt;安装dmgbuild、biplist

```
pip install dmgbuild
pip install biplist
```

2&gt;创建dmg\_setting.py

```
# encode=utf-8
from __future__ import unicode_literals

import biplist
import os.path

# pyinstaller -w -i xxx.icns xxx.py  # 打包app
# dmgbuild -s dmg_settings.py -D app=xxx.app "xxx" xxx.dmg  # 打包dmg

application = 'xxx.app'  # 待应用
drivers = 'xxx.dmg'  # 要加入的驱动
readme = 'Readme.txt'
appname = os.path.basename(application)


def icon_from_app(app_path):
    plist_path = os.path.join(app_path, 'Contents', 'Info.plist')
    plist = biplist.readPlist(plist_path)
    icon_name = plist['CFBundleIconFile']
    icon_root, icon_ext = os.path.splitext(icon_name)
    if not icon_ext:
        icon_ext = '.icns'
    icon_name = icon_root + icon_ext
    return os.path.join(app_path, 'Contents', 'Resources', icon_name)

# .. Basics ...................................................................

# Uncomment to override the output filename
# filename = 'mu-editor.dmg'
#
# Uncomment to override the output volume name
# volume_name = 'Mu Editor'
#
#
# Volume format (see hdiutil create -help)
format = 'UDBZ'


# Volume size
size = None

# Files to include
files = [application, drivers, readme]

# Symlinks to create
symlinks = {'Applications': '/Applications', }

# Volume icon
#
# You can either define icon, in which case that icon file will be copied to
# the image, *or* you can define badge_icon, in which case the icon file you
# specify will be used to badge the system's Removable Disk icon
#
# icon = '/path/to/icon.icns'
badge_icon = 'xxx.icns'

# Where to put the icons
icon_locations = {
    appname: (140, 120),
    'Applications': (500, 120),
    drivers:(140, 300)
}

# .. Window configuration .....................................................

# Background
#
# This is a STRING containing any of the following:
#
#    #3344ff          - web-style RGB color
#    #34f             - web-style RGB color, short form (#34f == #3344ff)
#    rgb(1,0,0)       - RGB color, each value is between 0 and 1
#    hsl(120,1,.5)    - HSL (hue saturation lightness) color
#    hwb(300,0,0)     - HWB (hue whiteness blackness) color
#    cmyk(0,1,0,0)    - CMYK color
#    goldenrod        - X11/SVG named color
#    builtin-arrow    - A simple built-in background with a blue arrow
#    /foo/bar/baz.png - The path to an image file
#
# The hue component in hsl() and hwb() may include a unit; it defaults to
# degrees ('deg'), but also supports radians ('rad') and gradians ('grad'
# or 'gon').
#
# Other color components may be expressed either in the range 0 to 1, or
# as percentages (e.g. 60% is equivalent to 0.6).
background = 'builtin-arrow'

# show_status_bar = False
# show_tab_view = False
# show_toolbar = False
# show_pathbar = False
# show_sidebar = False

sidebar_width = 180

# Window position in ((x, y), (w, h)) format
window_rect = ((100, 100), (640, 480))

# Select the default view; must be one of
#
#    'icon-view'
#    'list-view'
#    'column-view'
#    'coverflow'
#
default_view = 'list-view'

# General view configuration
show_icon_preview = False

# Set these to True to force inclusion of icon/list view settings (otherwise
# we only include settings for the default view)
include_icon_view_settings = 'auto'
include_list_view_settings = 'auto'

# .. Icon view configuration ..................................................

arrange_by = None
grid_offset = (0, 0)
grid_spacing = 100
scroll_position = (0, 0)
label_pos = 'bottom'  # or 'right'
text_size = 16
icon_size = 128

# .. List view configuration ..................................................

# Column names are as follows:
#
#   name
#   date-modified
#   date-created
#   date-added
#   date-last-opened
#   size
#   kind
#   label
#   version
#   comments
#
commentslist_icon_size = 16
list_text_size = 12
list_scroll_position = (0, 0)
list_sort_by = 'name'
list_use_relative_dates = True
list_calculate_all_sizes = False,
list_columns = ('name', 'date-modified', 'size', 'kind', 'date-added')
list_column_widths = {
    'name': 300,
    'date-modified': 181,
    'date-created': 181,
    'date-added': 181,
    'date-last-opened': 181,
    'size': 97,
    'kind': 115,
    'label': 100,
    'version': 75,
    'comments': 300,
}
#
list_column_sort_directions = {
    'name': 'ascending',
    'date-modified': 'descending',
    'date-created': 'descending',
    'date-added': 'descending',
    'date-last-opened': 'descending',
    'size': 'descending',
    'kind': 'ascending',
    'label': 'ascending',
    'version': 'ascending',
    'comments': 'ascending',
}

# .. License configuration ....................................................
# configurationwith open('LICENSE', 'rb') as f:
#     txt = f.read()
#
# license = {
#     'default-language': 'en_US',
#     'licenses': {
#         'en_US': txt,
#     },
#     'buttons': {},
# }
```

> 打包 dmg：   dmgbuild -s dmg\_settings.py -D app=xxx.app "xxx" xxx.dmg

7，压缩可执行文件

使用upx；

upx支持windows，linux32/64位；

使用方法：

```
Windows下：将upx.exe直接放置于PyInstaller的目录下
```

linux下使用指令：

```
python  pyinstaller.py  test.py --upx-dir  /home/test/upx/
```

8，mac下pyinstaller编译不稳定，可以使用py2app尝试。

完成跨平台软件，兼容性是个大问题，此外，使用python完成的项目，暂时编译成可执行文件之后文件太大，执行效率低下；

尤其是加入了GUI的项目，比如使用Qt之后的项目，编译成可执行文件执行速度和效率都降低。

跨平台项目最好选用C语言或者java，因为现有的编译器都很方便。

# 六、查看libcocoa.dylib

```
otoo -L libqcocoa.dylib
```





