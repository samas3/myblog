+++
date = '2026-07-23T19:33:36+08:00'
draft = false
title = '记录一次手机变砖+救砖的经历'
+++
## 背景
- 设备：realme GT Neo 5 SE

## 起因
本人心血来潮，想要动一动旧手机。在中午看到 [刷入国际版系统教程](https://ncunlock.com/how-to-unlock-sim-lock-on-realme-gt-neo-5se/) 后，果断尝试并成功刷入国际版 Color OS 13。而就在晚上，（此时我早就忘记系统已经不是原装），我又想到本设备解锁 Bootloader 后无法接收 OTA 更新，于是又尝试锁定 Bootloader。在我进入 Fastboot 后输入 `fastboot flashing lock` 且同意锁定后，我终于意识到我的左右脑互搏导致了严重的后果。

## 变砖后的症状
无法进入二屏，在一屏无限重启。无法进入 Fastboot / Recovery 模式。

## 救砖过程

### 准备工作
幸好~~在哪，这芯片的文件可难找了~~，这是一台骁龙 7+ Gen 2 设备，因此可以通过 9008 模式救砖。
感谢 [XDA 老哥](https://xdaforums.com/t/oppo-oneplus-realme-qualcomm-files-share.4769736/) 提供的文件。

需要准备的工具和材料有：

- 对应的 Flash (Stock) ROM 包 （需要 [Color OS 13](https://dfs-serverauto-in.allawnofs.com/dfs/23/11/23/c243f1a593384012912ec8e7bb1fc74d.zip)）
- Oplus EDL Tool
- 芯片对应的文件
- 驱动

### 操作流程
1. 进入 9008 模式
    - 关机状态下，按住音量+、音量-和电源键，连接数据线并插入电脑 USB 接口
2. 使用 Oplus EDL Tool 打开芯片对应的文件，进入 Firehose 模式
3. 加载 ROM 包并刷入
4. 重启设备
    - 第一次重启会进入 Recovery 模式
    - 选择“格式化数据分区”，结束后自动重启
    - 第二次重启后，可进入系统

## 遇到的问题
- 不能使用普通的线刷包，一定要使用带有 .xml 文件的 Stock ROM
- 目前设备无法进入 Fastboot 模式（短暂显示界面后自动重启）
    - 由于当前 Fastboot 仍处于锁定状态，因此可以接收到系统更新

## 后续
- 由于进行了数据清除操作，需要重新进行 [深度测试](https://magiskcn.com/realme-unlock-app.html)，以进入 Fastboot 模式
- 等待 7 天后，成功进入 Fastboot 模式，并解锁 Bootloader，救砖成功。

## 总结
不要脑子一热进行互斥的操作，一定记得在操作前清楚可能的后果。

刷机只能中午刷，因为早晚会出事。