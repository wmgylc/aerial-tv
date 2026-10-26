# XGIMI Z6X 第四代（XK03U / ironman / MStar m7221）

Android 8.0 / SDK 26，固件 `v1.1.21`；原生播放器 `com.xgimi.xhplayer` 1.4.2。实机验证日期：2026-10-26。

## HDMI 信号源

这台设备的 `TvInputManager` 没有输入，也没有注册 `TvInputService`。MStar 通用列表包含不存在的 HDMI2/3 和调谐器，不能用来生成端口。

普通应用可以反射读取 `com.xgimi.clients.GimiConfigManager.getInstance().getXgimiConfig("global")`。本机配置中只有一个 HDMI（`sw=HDMI`）和一个 USB2.0 媒体入口，所以桌面只添加 HDMI1。

HDMI 由已导出的 `com.xgimi.xhplayer/.InputActivity` 打开，使用 action `com.xgimi.action.hdmiPlayer` 及字符串 extra `launcher_start_source=HDMI1`。当前输入状态从 `TvCommonManager.GetInputSourceStatus()` 读取，按运行时 `TvOsType.EnumInputSource` 枚举 ordinal 对齐。没有打包厂商 JAR 或新增权限；接口不可用时继续原有的输入源回退。

已知 Z7X 的 Android 9 固件有另一条输入路径；这份适配只覆盖 Android 8 上实测的原生播放器协议。其他 Android 9 以上极米机型未验证。Z7X 的记录见 [xgimi-z7x.md](xgimi-z7x.md) 及相关 PR #437。

## 验证范围

- Aerial 1.50 的并行测试包中，首页及控制中心显示 HDMI1，遥控器点击进入极米原生播放器。该次即时 Home 返回验证时，`com.xgimi.home` 已停用；原官方 Aerial 安装和数据保留。
- 独立诊断程序以普通应用 UID、无额外权限验证过产品配置、输入状态和原生 HDMI Intent。
- 测试时没有有效 HDMI 输入信号，未验证视频、声音、HDCP 或 CEC。没有验证冷启动和待机唤醒。

用户观察：启用 `com.xgimi.home` 后按 Home，会先显示极米原桌面，几秒后 Aerial 守护才切回。即时返回的测试结果不能代表原桌面启用时的效果；HDMI 适配代码本身不处理这条接管延迟。

并行测试包使用独立包名，是因为本机没有上游 APK 的签名密钥；原官方 Aerial 包为并行运行而单独停用。这些测试部署设置与 HDMI 适配代码无关。HDMI 切换本身不要求停用极米原桌面。
