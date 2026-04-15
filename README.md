
# 安卓系统安装谷歌三件套
- Google 服务框架
- Google play service
- Google play store

### 首先尝试直接使用谷歌安装器安装

-  [GO谷歌安装器](https://www.pgyer.com/pOEI)
-  [SU谷歌安装器](https://www.wandoujia.com/apps/7971105)
-  [G谷歌服务安装](https://www.wandoujia.com/apps/8436358)
-  [HIGO谷歌安装器(支持部分华为)](https://www.wandoujia.com/apps/8124836)
-  [OurPlay(原谷歌空间)](https://www.wandoujia.com/apps/7661165)
-  [备用下载链接](https://github.com/sky22333/Google-Play-Store/releases)


### 小米，红米，一加等手机自带谷歌套件，可直接启用

[参考列表](https://storage.googleapis.com/play_public/supported_devices.html)

1：打开`设置`，搜索`谷歌`，启用`谷歌基础服务`

<div style="display:inline-block">
<img src="xiaomi/1.jpg" alt="1.jpg" width="300">
<img src="xiaomi/2.jpg" alt="2.jpg" width="300">
</div>

<br>

2：打开小米`应用商店`，搜索`google play`，安装`Google Play 商店`

<img src="xiaomi/3.jpg" alt="3.jpg" width="300">

---


> [!TIP]
> 下列通用方法 需要科学上网的支持


## 1：安装Google服务框架

[Google服务框架下载地址](https://www.apkmirror.com/apk/google-inc/google-services-framework/)

### 🔔注意一定要选择你的安卓系统版本的服务框架，根据文件名字来找，下载apk版本。

<img src="/png/google-play-framework-1.jpg" width="80%" />


<img src="/png/google-play-framework-2.jpg" width="80%" />


<img src="/png/google-play-framework-3.jpg" width="80%" />

---

## 2：安装Google play service

[Google play service下载地址](https://www.apkmirror.com/apk/google-inc/google-play-services/)

### 🔔注意一定要选择你的系统版本的下载，DPI也需要符合，点进去后下载最新版本，下载apk版本。

<img src="/png/google-play-service-01.jpg" width="80%" />


<img src="/png/google-play-service-02.jpg" width="80%" />


<img src="/png/google-play-service-03.jpg" width="80%" />


---

##  3：安装Google play store

[Google play store下载地址](https://www.apkmirror.com/apk/google-inc/google-play-store/)

### 🔔All Versions里面选择第一个最新版本就可以了，下载apk版本。

<img src="/png/google-play-store-01.jpg" width="80%" />


<img src="/png/google-play-store-02.jpg" width="80%" />


---

## 4：通用方法不生效？通过 ADB 将三件套安装为系统应用

> 适用场景：安卓模拟器、已 root 的设备、或直接安装后 GMS 无法正常初始化的情况。  
> 普通 `adb install` 只能装为用户级应用，GMS 必须作为**系统特权应用**放在 `/system/priv-app/` 才能正常工作。

### 前提条件

- 已安装 [ADB 工具](https://developer.android.com/tools/releases/platform-tools)
- 模拟器目录通常有预装Adb.exe，可直接用命令行调用。比如Shift+右键的，在此打开Powershell。
- 设备已开启 USB 调试 / 模拟器已开放 ADB 端口
- 设备已获得 root 权限
- `/system` 分区可写（见下方模拟器专项说明）

### 第一步：确认 ADB 连接

```powershell
# 实体设备通过 USB 连接后执行；模拟器替换为对应端口
adb devices
# 看到设备序列号或 127.0.0.1:端口 即为成功
```

### 第二步：确认系统架构和版本，下载匹配的 APK

```powershell
adb shell getprop ro.build.version.release   # 安卓版本，如 12
adb shell getprop ro.build.version.sdk       # SDK 版本，如 32
adb shell getprop ro.product.cpu.abi         # CPU 架构，如 x86_64 / arm64-v8a
```

前往 [APKMirror](https://www.apkmirror.com) 按照上述信息下载三个 APK，选择规则：
- `minAPI` 不超过你的 SDK 版本 （在Min~Target之间）
- ABI 架构与 `ro.product.cpu.abi` 一致（或选 `nodpi` 通用版）

### 第三步：获取 root 并推送 APK 到 priv-app

```powershell
adb root

# 创建系统特权应用目录
adb shell mkdir -p /system/priv-app/GoogleServicesFramework
adb shell mkdir -p /system/priv-app/GooglePlayServices
adb shell mkdir -p /system/priv-app/Phonesky

# 推送 APK（替换为你本地实际文件路径）
adb push "路径/gsf.apk"     /system/priv-app/GoogleServicesFramework/GoogleServicesFramework.apk
adb push "路径/gms.apk"     /system/priv-app/GooglePlayServices/GooglePlayServices.apk
adb push "路径/vending.apk" /system/priv-app/Phonesky/Phonesky.apk

# 设置正确权限
adb shell chmod 644 /system/priv-app/GoogleServicesFramework/GoogleServicesFramework.apk
adb shell chmod 644 /system/priv-app/GooglePlayServices/GooglePlayServices.apk
adb shell chmod 644 /system/priv-app/Phonesky/Phonesky.apk

# 重启设备
adb reboot
```

### 第四步：验证安装结果

```powershell
adb shell pm list packages | grep google
# 应看到：
# package:com.google.android.gsf
# package:com.google.android.gms
# package:com.android.vending
```

三件套出现在列表中，且应用内**没有卸载选项**（只有"停用"），说明已成功安装为系统应用。

---

### 🖥️ MuMu 模拟器 12 专项说明

MuMu Player 12 默认使用**只读系统盘**，需要先切换为可写模式才能操作 `/system`。

**开启可写系统盘：**

在 MuMu 模拟器主界面 → `设置` → `磁盘`（或磁盘相关选项）→ 将系统盘切换为**可写系统盘**，重启模拟器生效。

**验证是否可写：**

```powershell
adb shell "mount | grep system"
# 确认输出包含 (rw,...) 而非 (ro,...)
```

**完成安装后可改回只读：**

GMS 运行只需要 `/data` 分区的读写权限，`/system` 改回只读**不影响已安装的三件套正常使用**，反而能保护系统分区不被意外修改。建议完成安装后切换回只读模式。

> ⚠️ **注意**：MuMu 模拟器**升级**时会用新系统镜像覆盖 `/system`，三件套会丢失。建议升级前备份模拟器实例，或升级后重新执行上述推送步骤（保存好脚本，几分钟即可完成）。

---



>  所有的方法都不行的话还可以尝试备用方案[GBox虚拟空间](https://gboxlab.com) 支持华为鸿蒙系统

#### 替代方案（主流第三方APK商店）

- [APKMirror](https://www.apkmirror.com)

- [APKPure](https://apkpure.com/cn)
