---
title: "小米手机解锁 BL 并获取 Root 完整流程"
date: 2026-11-11
lastmod: 2026-11-11
draft: false
categories: ["教程"]
tags: ["刷机", "小米", "Root", "KernelSU", "HyperOS"]
summary: "从账号绑定、解锁 Bootloader 到刷入 KernelSU 获取 Root，完整走一遍小米手机的折腾流程。"
ShowToc: true
TocOpen: true
---

{{< tutorial device="小米 14 / 红米 K70 等" os="HyperOS 2.x" tools="Mi Unlock、ADB/Fastboot、KernelSU" difficulty="中等" time="约 1 小时（不含 7 天等待期）" risk="高" >}}

> **⚠️ 动手前必读**
> 解锁会**清空手机全部数据**，且可能影响保修。刷机有变砖风险，本文仅供有折腾需求的用户参考。
> 请先完整备份，并确保电量在 60% 以上。

## 一、准备工作

| 项目 | 说明 |
|---|---|
| 小米账号 | 需要满足解锁资格（社区等级、绑定时长） |
| 绑定等待 | 账号绑定设备后需等待 **7 天**（部分机型 168 小时） |
| 电脑环境 | Windows 10/11，装好小米 USB 驱动 |
| 工具 | Mi Unlock Tool、platform-tools（ADB/Fastboot） |
| 固件 | 与当前系统**完全一致**版本的 boot / init_boot 镜像 |

> 固件一定要对应机型 + 系统版本，刷错分区是变砖最常见的原因。

## 二、开启开发者选项

1. 设置 → 我的设备 → 全部参数 → 连续点击「MIUI/HyperOS 版本」7 次
2. 返回设置 → 更多设置 → 开发者选项
3. 打开 **USB 调试** 和 **USB 安装**
4. 打开 **OEM 解锁**（部分机型叫「设备解锁状态」）

## 三、绑定账号并等待

在「开发者选项 → 设备解锁状态」里登录小米账号并绑定。

绑定成功后，需要等待 7 天才能解锁。这期间：

- 不要退出账号
- 不要恢复出厂设置
- 不要刷入任何第三方系统

## 四、解锁 Bootloader

```bash
# 1. 手机关机，同时按住 音量下 + 电源，进入 Fastboot
# 2. 数据线连接电脑，确认设备被识别
fastboot devices

# 应输出类似：
# 4c1f9a2b        fastboot
```

打开 **Mi Unlock Tool**：

1. 登录同一个小米账号
2. 点击「解锁」
3. 等待进度条走完，手机会自动重启并清空数据

> 首次解锁可能需要多次尝试（提示「绑定时间不足」就继续等）。

## 五、获取 Root（KernelSU 方案）

Root 的本质是**修改 boot 分区**，让系统在启动时加载提权模块。

### 5.1 提取原厂镜像

从官方固件包里解出 `init_boot.img`（部分老机型是 `boot.img`）：

```bash
# 解包固件（以 payload.bin 为例）
payload-dumper-go payload.bin -o extracted

# 需要的文件
ls extracted/init_boot.img
```

### 5.2 修补镜像

1. 把 `init_boot.img` 传到手机
2. 打开 **KernelSU** App → 安装 → 选择「未安装」→ 选择镜像文件
3. 生成 `kernelsu_patched_xxxx.img`，传回电脑

### 5.3 刷入

```bash
# 进入 Fastboot
adb reboot bootloader

# 刷入修补后的镜像（注意分区名）
fastboot flash init_boot kernelsu_patched_xxxx.img

# 重启
fastboot reboot
```

> 分区名必须是 `init_boot`（Android 13+ 机型）或 `boot`（更早机型），
> 刷错分区会导致无法开机。

## 六、验证

开机后打开 KernelSU，如果显示「工作中」，说明 Root 成功。

可以用终端确认：

```bash
adb shell
su -c id
# 应输出 uid=0(root)
```

## 七、常见问题

**Q：解锁后还能收到 OTA 吗？**

可以，但 OTA 会覆盖 boot 分区，导致 Root 失效。系统更新后需要**重新提取新版本镜像并重复第五步**。

**Q：提示「绑定时间不足」怎么办？**

说明 7 天等待期没到。注意不要在这期间退出账号或恢复出厂，否则计时重置。

**Q：刷完开不了机（变砖）怎么办？**

多数情况是分区刷错或镜像不匹配。进入 Fastboot 重新刷回**原厂 init_boot**即可恢复正常；只要 Fastboot 还能进去，基本都能救回来。

**Q：解锁会影响保修吗？**

官方口径是解锁后部分保修条款失效，具体以售后检测为准。

## 八、小结

整个流程的核心就三步：

```
绑定账号等 7 天  →  解锁 BL  →  修补 boot 并刷入
```

真正容易出错的不是操作，而是**镜像版本对不上**。动手前把固件、工具、备份都准备好，成功率会高很多。
