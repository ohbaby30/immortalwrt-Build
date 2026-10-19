# ImmortalWrt GitHub Actions 自动编译

![Build R4S](https://github.com/ohbaby30/immortalwrt-Build/actions/workflows/build-r4s.yml/badge.svg)
![Build x86_64](https://github.com/ohbaby30/immortalwrt-Build/actions/workflows/build-x86_64.yml/badge.svg)

本仓库使用 GitHub Actions 自动编译 ImmortalWrt `master` 固件，支持 x86_64 和 FriendlyARM NanoPi R4S 两个目标平台，集成 Passwall2 + Xray。

## 构建目标

| 平台 | 配置文件 | 自定义文件 | 首次启动 LAN IP | 固件文件名 |
|------|----------|------------|-----------------|------------|
| x86_64 | `configs/x86_64.config` | `files-x86_64/` | `10.10.10.123/24` | `immortalwrt-x86-64-generic-squashfs-combined-efi.img.gz` |
| R4S | `configs/r4s.config` | `files-r4s/` | `192.168.50.254/24` | `immortalwrt-rockchip-armv8-friendlyarm_nanopi-r4s-squashfs-sysupgrade.img.gz` |

两个目标的软件包配置一致，Passwall2 使用 Xray 核心。

## ⚠️ 安全警告

固件首次启动时 **root 密码为空**（无密码），方便调试但存在安全风险。

**刷入固件后，请立即通过 SSH 或 LuCI 设置 root 密码：**

```sh
passwd root
```

请勿在设置密码前将设备暴露在公网或不受信任的网络中。

## 使用方法

1. 将本仓库内容（不含外层目录）上传到一个 GitHub 仓库根目录
2. 打开 `Actions` 页面，选择对应的 x86_64 或 R4S 工作流
3. 点击 `Run workflow` 手动触发编译

也可以等待每周日凌晨 02:00（北京时间）的自动定时编译。

## 构建产物

每次构建会发布以下文件到 GitHub Releases：

- 对应平台的固件镜像（`.img.gz`）
- Passwall2 的 `.apk` 安装包
- `SHA256SUMS` 校验和文件，用于验证下载完整性

## PassWall2 在线更新

安装本仓库新构建的固件后，PassWall2 自身的“检查更新”“更新”“强制更新”使用本仓库 APK 和中文包；Xray、Sing-box 等核心组件保留各自的上游更新方式。

- 每周日北京时间 02:00 自动拉取上游源码，替换 PassWall2 自身更新入口，自动设置本仓库构建版本，无需跟随上游手动修改版本号。
- 主包版本为 `YYYYMMDD.<Actions run ID>.<attempt>-r1`，中文包使用相同的前三段版本；构建日期按运行创建时间转为北京时间。
- 两个平台都内置相同更新地址。默认 `master`、默认配置的 x86_64 构建负责发布一套通用 APK；R4S 不竞争发布指针，x86_64 构建失败时更新源保留上一次成功版本。
- 更新 JSON：[passwall2-release-api.json](https://raw.githubusercontent.com/ohbaby30/immortalwrt-Build/passwall2-update/passwall2-release-api.json)。首次成功构建发布前该地址尚不存在。GitHub 原始文件缓存可能使检查更新延迟数分钟。
- 发布脚本先检查实际 APK 的版本、`noarch` 架构和包内更新入口，再校验远程附件大小及 SHA-256，全部成功才提交 `passwall2-update` 分支的 JSON。较旧运行不会覆盖较新版本。
- 更新包存入独立的 `passwall2-*` Release，不随旧固件 Release 一起清理；保留历史版本用于回退。不要删除当前 JSON 引用的插件 Release。
- 手动选择其他系统分支或配置时仍编入本仓库更新入口，但不会替换公共更新源；这些自定义系统与公共 APK 的依赖兼容性需自行验证。

### 已安装旧固件时首次切换

不必为了修改更新源而重刷固件。首次成功构建后，从本仓库对应的 `passwall2-*` Release 下载主程序 APK 和中文包，在设备上手动安装一次；此后可直接在 PassWall2 页面更新。先核对当前系统使用 APK 且依赖兼容，并导出 PassWall2 配置备份。旧版插件不会因仓库代码改变而自动切换入口。

首次切换后包版本使用本仓库规则；恢复官方包需要显式选择官方 APK 并处理版本降级，不能依靠普通检查自动回退。APK 更新沿用上游安装器及其 `--allow-untrusted` 行为，不改变设备的软件源或签名密钥。

## 🙏特别感谢

本项目源自于 [ImmortalWrt](https://github.com/immortalwrt/immortalwrt) 和 [PassWall2](https://github.com/Openwrt-Passwall/openwrt-passwall2)。

感谢 [ImmortalWrt](https://github.com/immortalwrt/immortalwrt) 和 [PassWall2](https://github.com/Openwrt-Passwall/openwrt-passwall2) 等项目以及所有插件作者对开源路由所作的贡献。
