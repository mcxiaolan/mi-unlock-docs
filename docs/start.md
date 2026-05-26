# 开始解锁
## 准备文件
- [降级包](https://www.coolapk.com/feed/71401154) | [本站备份](https://files.ltya.top/E3/Flash/mi-8e-unlock/3_downgrade)
- 线刷包  
    https://xiaomirom.com  
    https://miuiver.com
- [解锁文件](https://www.coolapk.com/feed/71401154) | [本站备份](https://files.ltya.top/E3/Flash/mi-8e-unlock/4_unlock)

相应步骤和脚本已经放在[我的网盘](https://files.ltya.top/E3/Flash/mi-8e-unlock)，需要自取。

## 推送 abl
将降级包中的 abl.elf 复制到你喜欢的位置，或者直接在 `images` 里打开终端。
    
=== ":material-linux: Linux/Termux"
    ```bash
    sudo adb push ./abl.elf /data/local/tmp/abl.elf
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-adb push ./abl.elf /data/local/tmp/abl.elf
    ```

## 刷入 abl
!!! warning "警告"

    刷入后手机将无法正常启动，同时在 FASTBOOT 模式下可以在未解锁的情况下刷写分区。

=== ":material-linux: Linux/Termux"
    ```bash
    sudo adb shell
    ```
    ```bash
    # 查看是否成功推送
    ls /data/local/tmp
    ```
    ```bash
    service call miui.mqsas.IMQSNative 21 i32 1 s16 "dd" i32 1 s16 'if=/data/local/tmp/abl.elf of=/dev/block/by-name/abl_a' s16 '/data/mqsas/log.txt' i32 60
    service call miui.mqsas.IMQSNative 21 i32 1 s16 "dd" i32 1 s16 'if=/data/local/tmp/abl.elf of=/dev/block/by-name/abl_b' s16 '/data/mqsas/log.txt' i32 60
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-adb shell
    ```
    ```bash
    # 查看是否成功推送
    ls /data/local/tmp
    ```
    ```bash
    service call miui.mqsas.IMQSNative 21 i32 1 s16 "dd" i32 1 s16 'if=/data/local/tmp/abl.elf of=/dev/block/by-name/abl_a' s16 '/data/mqsas/log.txt' i32 60
    service call miui.mqsas.IMQSNative 21 i32 1 s16 "dd" i32 1 s16 'if=/data/local/tmp/abl.elf of=/dev/block/by-name/abl_b' s16 '/data/mqsas/log.txt' i32 60
    ```

如果返回类似 `Parcel 000000` 之类的提示，说明 abl 已经正确替换

=== ":material-linux: Linux/Termux"
    ```bash
    sudo adb reboot bootloader
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-adb reboot bootloader
    ```

## 工程包处理
### 编辑刷机脚本
1. 解压你的机型的工程包（降级小包），编辑包内的 `flash_all.bat`
2. 删除图中无用部分和批处理脚本中被注释的内容（Windows 的注释方法和 Linux 不一样）
    ![1.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/1.webp)
    正确删除后如下：
    ![2.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/2.webp)

3. 点击右上角三个点，点击搜索，打开正则表达式，查找文本框填入 `(^.+)(\s\|{2}.+$)`, 替换文本框填入 `$1`.
    ![3.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/3.webp)
    然后点击全部替换，结果应该如下图
    ![4.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/4.webp)

4. 关闭正则表达式，查找 `%~dp0`, 替换为 `./`, 全部替换。
    ![5.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/5.webp)

5. 查找 `%* `(注意后面或前面带上空格)，替换文本框留空，全部替换
    ![6.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/6.webp)

6. 如果有 `\`, 替换为 `/`.
    ![7.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/7.webp)

7. 最后，删除最后一行的 reboot 指令，最终呈现的应该如图所示
    ![8.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/8.webp)

8. （无 Root）将 `fastboot` 替换为 `termux-fastboot`
9. 保存文件，将文件名更改为 `flash_all.sh`

### 刷入工程包
!!! info

    以下教程成功的前提是您将包放到了 `/storage/emulated/0/Download/ROM/` 下，否则您需要自行更改命令中的位置。
    ![Screenshot_2026-05-26-20-09-37-585_bin.mt.plus-edit.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/Screenshot_2026-05-26-20-09-37-585_bin.mt.plus-edit.webp)
    
=== ":material-linux: Linux/Termux"
    ```bash
    cd ~/storage/downloads/ROM/
    sudo bash flash_all.sh
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    cd ~/storage/downloads/ROM/
    bash flash_all.sh
    ```

如果看到上面的分区均 flash successfully 并且最后正确切换 A 槽位，那么可以重启继续操作。

=== ":material-linux: Linux/Termux"
    ```bash
    sudo fastboot reboot
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-fastboot reboot
    ```

之后应该进入的是蓝色的 FASTBOOTD 模式。  
如果你没有看到屏幕显示画面请不要慌张，有可能是你的机型不会显示，执行 `fastboot devices` 即可看到设备正常连接。

=== ":material-linux: Linux/Termux"
    ```bash
    sudo fastboot reboot bootloader
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-fastboot reboot bootloader
    ```

## 使用特制 boot 解锁
删除 ROM 文件夹内的所有文件，将修改好的 GPT 分区和 boot 放入。
![Screenshot_2026-05-26-20-52-19-301_bin.mt.plus-edit.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/Screenshot_2026-05-26-20-52-19-301_bin.mt.plus-edit.webp)

=== ":material-linux: Linux/Termux"
    ```bash
    sudo fastboot flash partition:4 gpt_both4.bin
    sudo fastboot boot boot.img
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-fastboot flash partition:4 gpt_both4.bin
    termux-fastboot boot boot.img
    ```

若终端提示您 boot 成功，则恭喜你的设备极大概率已经解锁。  
接下来，按住 电源键+音量减 直到进入 FASTBOOT(约按住 12s)

## 验证解锁状态
=== ":material-linux: Linux/Termux"
    ```bash
    sudo fastboot getvar unlocked
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-fastboot getvar unlocked
    ```

若返回 `unlocked: yes` 则恭喜您顺利解锁您的设备。

## 线刷官方包
!!! warning "警告" 

    如果跳过此步骤刷入官改或其他第三方包导致变砖，本人概不负责。

接下来我们来恢复官方 abl 和分区表。  
删除 ROM 文件夹内的所有文件，将线刷包内的文件放入 ROM 文件夹，确保能看到 images 文件夹。
![Image_1779802499158_143_1779802599485edit.jpg](https://img.cf.cdn.flyyoung.org/file/mi-8e-unlock/start/Image_1779802499158_143_1779802599485edit.webp)
返回 Termux.

=== ":material-linux: Linux/Termux"
    ```bash
    sudo fastboot flash abl_ab ./images/abl.elf
    sudo fastboot flash partition:0 ./images/gpt_both0.bin
    sudo fastboot flash partition:1 ./images/gpt_both1.bin
    sudo fastboot flash partition:2 ./images/gpt_both2.bin
    sudo fastboot flash partition:3 ./images/gpt_both3.bin
    sudo fastboot flash partition:4 ./images/gpt_both4.bin
    sudo fastboot flash partition:5 ./images/gpt_both5.bin
    sudo fastboot reboot bootloader
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    ```bash
    termux-fastboot flash abl_ab ./images/abl.elf
    termux-fastboot flash partition:0 ./images/gpt_both0.bin
    termux-fastboot flash partition:1 ./images/gpt_both1.bin
    termux-fastboot flash partition:2 ./images/gpt_both2.bin
    termux-fastboot flash partition:3 ./images/gpt_both3.bin
    termux-fastboot flash partition:4 ./images/gpt_both4.bin
    termux-fastboot flash partition:5 ./images/gpt_both5.bin
    termux-fastboot reboot bootloader
    ```

重启后，执行以下命令即可开始刷机。
=== ":material-linux: Linux/Termux"
    ```bash
    sudo bash flash_all.sh
    ```

=== ":fontawesome-solid-terminal: Termux without ROOT"
    请编辑 `flash_all.sh`, 将 `fastboot` 替换为 `termux-fastboot`.
    ```bash
    bash flash_all.sh
    ```