---
layout: post
title: 关于如何使用Inno Setup来打包Windows后台服务应用的方法
categories:
  - Technology
description: 关于如何使用Inno Setup来打包Windows后台服务应用的方法
keywords: 'Inno Setup, 打包Windows Server, 打包windows后台应用, windows后台应用打包'
comments: true
---

# 关于如何使用Inno Setup来打包Windows后台服务应用的方法

```
系统：Windows 7
Inno Setup: 5
Python: 3.7
Pyinstaller: 4.0
```

InnoSetup 是一个免费的 Windows 安装程序制作软件，可以将我们写的好的程序打包成可安装的exe软件。本篇文章以Python程序为例，将讲述如何使用InnoSetup打包一个在Windows后台运行的可安装程序。

### 1. 编写后台运行入口程序

**main.py**

```python
import platform
import sys
import servicemanager
from mytestserver import *


if __name__ == '__main__':
    if platform.system() == "Windows":
        if len(sys.argv) == 1:
            servicemanager.Initialize()
            servicemanager.PrepareToHostSingle(MytestServer)
            servicemanager.StartServiceCtrlDispatcher()
        else:
            win32serviceutil.HandleCommandLine(MytestServer)
```

**mytestserver.py**

```
import win32serviceutil
import win32service
import win32event
import logging
import config
import os
import sys

class MytestServer(win32serviceutil.ServiceFramework):
    _svc_name_ = "mytest"  # 服务名
    _svc_display_name_ = "mytest"  # 服务在windows系统中显示的名称
    _svc_description_ = "服务测试"  # 服务的描述

    def __init__(self, args):
        win32serviceutil.ServiceFramework.__init__(self, args)
        self.stop_event = win32event.CreateEvent(None, 0, 0, None)

    def SvcDoRun(self):

        self.ReportServiceStatus(win32service.SERVICE_START_PENDING)
        try:
            self.ReportServiceStatus(win32service.SERVICE_RUNNING)
            import time
            time.sleep(3)
            
            --------------------------
            此处编写或调用要进行后台运行的程序、服务
            --------------------------
            
            win32event.WaitForSingleObject(self.stop_event, win32event.INFINITE)
        except BaseException as error:
            self.SvcStop()

    def SvcStop(self):
        self.ReportServiceStatus(win32service.SERVICE_STOP_PENDING)
        win32event.SetEvent(self.stop_event)
        self.ReportServiceStatus(win32service.SERVICE_STOPPED)

```

###2. Inno Setup打包文件

Inno Setup打包文件执行的时候会根据内容指令来进行程序的打包、静态文件的加载以及程序的相关配置等

```
#define MyAppName "MyTest"
#define MyAppVersion "1.1.0"
#define MyAppPublisher "mytest测试程序"
#define MyAppExeName "MyTest.exe"

[Setup]
; NOTE: The value of AppId uniquely identifies this application.
; Do not use the same AppId value in installers for other applications.
; (To generate a new GUID, click Tools | Generate GUID inside the IDE.)
AppId={{A0E7869D-47DB-4FDD-A031-B6747D3CBF6F}}
AppName={#MyAppName}
AppVersion={#MyAppVersion}
;AppVerName={#MyAppName} {#MyAppVersion}
AppPublisher={#MyAppPublisher}
DefaultDirName={pf}\MyTest
DisableProgramGroupPage=yes
DefaultGroupName={#MyAppName}
OutputDir=C:\Users\Administrator\Desktop
OutputBaseFilename=MyTest Setup V{#MyAppVersion}
SetupIconFile=xxx\favicon.ico
Compression=lzma
SolidCompression=yes
ArchitecturesInstallIn64BitMode=x64 ia64

[Languages]
Name: "english"; MessagesFile: "compiler:Default.isl"
Name: "chinesesimplified"; MessagesFile: "compiler:Languages\ChineseSimplified.isl"


[Tasks]
Name: "desktopicon"; Description: "{cm:CreateDesktopIcon}"; GroupDescription: "{cm:AdditionalIcons}"; Flags: checkablealone

[Files]
Source: "dist\MyTest\*"; DestDir: "{app}"; Flags: ignoreversion recursesubdirs createallsubdirs
; NOTE: Don't use "Flags: ignoreversion" on any shared system files

[Icons]
Name: "{group}\MyTest"; Filename: "{app}\MyTest.exe"



[Run]
Filename:"{app}\{#MyAppExeName}"; Parameters:" stop"; Flags: runhidden
Filename:"{app}\{#MyAppExeName}"; Parameters:" remove"; Flags: runhidden
Filename:"{app}\{#MyAppExeName}"; Parameters:" --startup delayed install"; Flags: runhidden
Filename:"{app}\{#MyAppExeName}"; Parameters:" start"; Flags: runhidden

[UninstallRun]
Filename:"{app}\{#MyAppExeName}"; Parameters:" stop"; Flags: runhidden
Filename:"{app}\{#MyAppExeName}"; Parameters:" remove"; Flags: runhidden
```

> 各指令如上所示，言简意赅不再赘述