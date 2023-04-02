For English user, I am so tired to make it and write this and have no more time and energy to write an English version...So please use translate (E.g. Google Translate) to read it. Thank you!

# 简介
这可能是全网最完美的HP暗影精灵2 Pro的黑苹果了，理论上HP的Kaby Lake & NVIDIA 10系显卡的游戏本都通用（暗影3好像也是这个配置），什么15-AX218TX/AX214TX，包括17寸的，因为感觉同一家厂商相似产品差别不会太大嘛。
基于Clover 5151版本，使用时可以自己升级到最新版Clover。喜欢使用OpenCore的同学可以自己拿这个配置作为参考转换过去。

### 其它事项

目前我使用的是最新版 macOS 13 Ventura，之前用10.15 Catalina，所以理论上Catalina ~ Ventura这之间的版本都能用。更早的理论上应该都没问题。如果你想用Ventura之外的版本，请自行检查外设Kext的兼容性（尤其是Intel WIFI和蓝牙的，尤其是AirportItlwm.kext，它每个版本的macOS有不同的驱动，除了这个之外其它似乎和macOS版本相关性不大）。

FileVault驱动我没加，因为我不用，需要的自己加。

Hfs驱动也没加，现在不都是APFS了嘛。需要的自己加。

里面的序列号啥的是用Clover自动生成的，用的时候请自己再重新生成一个新的，以免和别人撞号。

### 关于睡眠一段时间后自动唤醒的问题

在Catalina时没发生过意外唤醒，但在升级Ventura之后确实会睡眠2~3小时之后自动唤醒。网上查了一通，这个应该是Ventura的Feature，好像跟查找我的Mac有关，跟黑苹果没有关系，因为看到论坛里白果Macbook用户也被这个问题困扰。

可以用 ```pmset -a tcpkeepalive 0``` 禁用掉这个feature。

# Features
## 电源/机器本身功能相关（这也是Tweak的重点）

1. 睡眠唤醒正常！

2. USB正常，USB3.0能达到5Gb/s，速率正常。使用USBMap.kext & SSDT，已做好USB映射。

3. 完美屏蔽独显，包括睡眠唤醒之后！（重磅！这个东西折腾了我好几年！），空闲状态适配器端待机功耗17W左右（关键盘背光，亮度100%），后面讲这个如何实现的。

4. 亮度调节正常，关机重启可记住上次亮度（AppleBacklightFixup.kext & SSDT-PNLF）。

5. CPU睿频正常，最低CPU频率800MHz，节能省电（CPUFriend.kext & CPUFriendDataProvider.kext）。

6. 电池电量显示正常，充电状态显示正常 （ACPIBatteryManager.kext）。

7. Fn热键正常（Fn+F2/F3亮度，F6 F7 F8音量，F9 F10 F11多媒体正常，F4因为是切换显示器键，映射到了Win+P，macOS下也就是...Cmd+P也就变成了打印...要改自己改了，F1 F12无功能但也可以 自己改，使用的VoodooPS2Controller.kext）

8. Lid功能正常（合盖自动睡眠）

9. 启动速度很快，在我的机器上Apple Logo走进度条也就5秒左右就到登录界面了（当然跟你的硬盘有关）。

## 外设篇

1. 声卡用的最新版从Github Release下的预编译好的AppleALC.kext，layout-id=13写在了ACPI里。扬声器正常，麦克风正常，耳机口正常。
    从Windows下重启到macOS之后无声&CPU占用偏高这个问题我没修，折腾了一下没弄好懒得弄了，如果有完美的方案欢迎提交PR或者issue。而且这个问题的罪魁祸首是Windows的Realtek驱动，重启到别的系统比如Ubuntu或者FreeBSD等也存在这个问题，不关黑苹果的事，索性不修了，如果要切系统关机再开机就好了。

2. Intel WiFi. 塞了AirportItlwm.kext v2.2.0-alpha for Ventura进去
    这个是个alpha的版本，好像在Ventura下还不太稳定，但是我用起来还算OK，包括睡眠唤醒之后WIFI都算正常，就是有时候联网速度慢一点（可能要过个几十秒才连上WIFI），但我目前还没出现网上别人说的超过5分钟都连不上WIFI的情况，不过issue里确实挺多人反馈这个问题的。非常在意WIFI稳定性的可以考虑低版本系统。
    另外，无意间发现在Ventura下，用上述的WIFI驱动，并且蓝牙也能正常工作的情况下，iPad可以一键无线随航，用起来也不卡。之前用Catalina的时候就不行，无线随航死活打不开，只能插USB。
    
3. 蓝牙 用的BlueToolFixup.kext & IntelBTPatcher.kext & IntelBluetoothFirmware.kext
    参考的这个：https://openintelwireless.github.io/IntelBluetoothFirmware/FAQ.html#what-additional-steps-should-i-do-to-make-bluetooth-work-on-macos-monterey-and-newer
    目前好像有个小问题，蓝牙好像有时候关掉了就打不开了，只能重启后才能打开（我不在意这个，反正不关就是了）。在意这个的可以考虑低版本系统，比如BigSur，然后上IntelBluetoothFirmware.kext & IntelBluetoothInjector.kext。之前这个组合在Catalina一切正常，能开能关。

4. 触摸板 VoodooPS2Controller.kext。模拟Apple原生触摸板，能用所有手势。

5. 有线网卡 RealtekRTL8111.kext 工作正常。

6. Realtek SD卡读卡器 Sinetek-rtsx.kext，加上几个Kernel启动参数，工作正常，速度挺快。

### 已知问题

1. 进入睡眠的速度有一丢丢慢，大概要30秒左右才能进入睡眠。这个我没有头绪了。

2. 按电源键有时会弹出这个对话框，而不是直接进入睡眠状态，有时候又能直接睡眠，但感觉这个应该和EFI啥的没关系，不知道是不是我的这个macOS的什么设置问题，知道的朋友麻烦给我支个招~

![图片](https://user-images.githubusercontent.com/18359157/229378490-012d7819-3396-4bc4-82da-eea5e885e194.png)


其它好像没发现啥问题了

# 关于NVIDIA独显屏蔽

这个机器有些奇怪，用常规方法（修改PTS/WAK）唤醒后没法屏蔽独显，导致功耗增加5W左右。

这台机器知道独显有没有成功关闭很简单，看功耗就知道。在充满电的情况，电源适配器交流电那端插个功耗指示表（这玩意挺好用的，知道现在整机功耗，进而知道CPU/显卡状态），看整机功耗多少。

在全新Win10系统下，打完驱动，NVIDIA显卡关闭状态下，键盘灯关闭，亮度100%，空闲状态下，整机功耗为17W左右。Linux下把独显关闭后也是这个功耗。可以得知这台机器的一个基本闲置功耗（独显不工作）。如果确保CPU空闲，不在充电状态，功耗>20W基本就说明独显没关闭，在Linux下也是如此。

这个在黑苹果下睡眠唤醒后独显无法屏蔽的问题困扰了我好几年，也是这个导致我一直没法把macOS当主力系统用的原因（出门在外肯定要睡眠唤醒，一唤醒独显不关白白多5W功耗，续航打好几折）。一直在不断地试，前几天终于把这个问题完美解决了，这也是我决定把这套配置发出来的原因。

说简单也很简单，直接上ACPI代码（这个写在了SSDT-PRW.aml里）

```
    Scope (\_SB.PCI0.PEG0.PEGP)
    {
        OperationRegion (PCIS, PCI_Config, Zero, 0x0100)
        Field (PCIS, AnyAcc, NoLock, Preserve)
        {
            PVID,   16, 
            PDID,   16
        }

        Method (_PRW, 0, NotSerialized)  // _PRW: Power Resources for Wake
        {
            \_SB.PCI0.PEG0.PEGP._OFF ()
        }
    }
```
其实就是Hook显卡的_PRW方法，在调用它的时候直接把它关了就行。

同时_PTS/_WAK也还是要的，要在_PTS时_ON()，在_WAK时_OFF()一下（虽然不起作用）

感觉这台机子可能在WAK之后做了些其它什么事情，就算你在_WAK里把独显OFF了，之后什么机制又把它唤醒起来了。

前几天想到这个问题又翻出来折腾一翻，于是又去挖一下这台机子原生的DSDT及SSDT代码，看看能不能找到什么蛛丝马迹，目的主要是找除了_WAK之外的Hook点，在那个点上把独显OFF掉。于是乎发现了显卡设备下面自己有个_PRW，虽然不知道具体是干啥的，但是看MaciASL给的注释“Power Resources for Wake”，感觉和Wake有关，于是直接在这个里面OFF()，试了下居然成功了！
