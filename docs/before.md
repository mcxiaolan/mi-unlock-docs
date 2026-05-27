# 在开始前

!!! warning

    请您了解解锁设备后所带来的风险，再决定是否需要解锁。  
    一旦操作不当，您的设备可能将无法启动。
    
## 配置调试环境
### Windows

!!! warning

    尽量避免使用网吧里的电脑！  
    那里的电脑硬件大多经过了魔改或者限制，连接手机后可能仅会充电或者无法识别设备类型。  
    如果您只能使用它来解锁，请务必打开**设备管理器**，查找您的设备并更新驱动。  
    建议点击菜单里加入讨论组以寻求此方面的帮助。

您需要准备以下文件： 

- [platform-tools](https://android-docs.cn/tools/releases/platform-tools#downloads)
- [usb-driver](https://android-docs.cn/studio/run/win-usb)[^1]

[^1]: 如果您正在使用最新的 Windows 系统并且保持更新，一般可以不用安装此驱动，因为 Windows 已内置。

#### 添加环境变量
1. 解压 platform-tools-latest-windows.zip
2. 进入 platform-tools 文件夹
3. 右键地址栏，点击**复制地址**![1](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/before/1.webp)
4. 右键此电脑，点击**属性**以打开设置
5. 在设置中，点击**高级系统设置**以打开系统属性
6. 在系统属性中，点击**环境变量**
7. 在新打开的窗口中找到**系统变量的 Path 变量**，点击编辑![2](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/before/2.webp)
8. 在新打开的窗口中点击新建，粘贴刚刚复制的 platform-tools 文件夹目录，点击确定三次

#### 验证配置
打开**终端**或使用++win+r++打开 cmd  
运行 `adb help` 和 `fastboot help`  
如果能正确打印使用方法则代表您已正确配置。  

#### 安装驱动
1. 解压 usb_driver_r13-windows.zip
2. 进入 usb_driver 文件夹
3. 右键 android_winusb.inf, 点击安装
4. 在新弹出的窗口中点击安装![3](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/before/3.webp)
5. 手机进入 FASTBOOT 模式并连接电脑，运行 `fastboot devices`, 如果能识别设备则驱动正确配置
6. 如果没有，请看[这篇文章](https://www.cnblogs.com/changweijinghu/p/16880803.html)解决

### Android/Linux
!!! info

    此方法需要熟悉 Linux 操作，适合熟悉命令行的用户或者身边没有适合的 Windows 设备用户使用。  
    整个过程建议以 Root 用户身份运行。

!!! warning

    如果不熟悉操作，请不要使用此方法！  
    或者加入讨论组以寻求此方面的帮助。

Android 设备需要准备以下软件：

- [Termux](https://github.com/termux/termux-app)
- [Termux:API](https://github.com/termux/termux-api) (无 Root 时需要)
- 关闭它们的电池优化

#### 安装软件包
1. Termux
    ```bash
    # 授予 Termux 所有文件访问权限
    termux-setup-storage
    # 换源（开代理可忽略此步骤）
    termux-change-repo
    # 空格键或触屏选中，上下键切换选项，回车确认
    apt upgrade
    # 如果询问，建议全选 Y
    # 更新完软件包后，您的源就会恢复成官方源，需要的话可以再执行一次换源操作
    ```

    !!! info "Termux 拥有 Root 权限"
    
        ```bash
        apt install android-tools tsu -y
        ```
        当涉及操作被调试设备（如查看是否正确连接处于 FASTBOOT 的设备）时，请在命令前加入 **sudo**
        ```bash
        # 如: fastboot devices
        # 请使用以下命令
        sudo fastboot devices
        # 刷机
        sudo bash flash_all.sh
        ```
        ![termux-3](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/before/termux-3.webp)
        
        !!! warning
        
            如果你使用 Magisk 来管理 ROOT 权限，则可能提权失败，这是 tsu 的问题，请看[这篇文章](https://blog.iamsjy.com/2025/10/30/solve-termux-sudo-tsu-privilege-escalation-issue-under-magisk-alpha/)解决。

    !!! info "Termux 没有 Root 权限"
    
        （请确认您能访问 Github）
        ```bash
        curl -s https://raw.githubusercontent.com/nohajc/termux-adb/master/install.sh | bash
        ```
        > 允许 Termux:API 访问您的设备
        ![termux-1](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/before/termux-1.webp)
        
        当使用 `adb` 和 `fastboot` 命令时，请在命令前加入 `termux-`
        ```bash
        # 如: fastboot devices
        # 请使用以下命令
        termux-fastboot devices
        # 如: adb devices
        # 请使用以下命令
        termux-adb devices
        ```
        ![termux-2](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/before/termux-2.webp)
        
        !!! warning
    
            刷机脚本 flash_all.sh 里的 fastboot 指令，您应该全部替换为 termux-fastboot
            ![termux-4.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/before/termux-4.webp)

2. Linux
    ```bash
    apt install android-tools -y
    ```


## 验证是否可以解锁
将 SELinux 设置为**宽容模式**是解锁的基础，请用数据线连接设备并进入 FASTBOOT 模式。执行以下命令
```bash
fastboot oem set-gpu-preemption 0 androidboot.selinux=permissive
fastboot continue
```
手机打开 USB 调试，确认好权限后执行
```bash
adb shell getenforce
```
若返回 `Permissive` 则可以开始解锁您的设备
