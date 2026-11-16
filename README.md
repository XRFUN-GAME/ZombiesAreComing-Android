# MoRiGame - Android 导出工程

由 Unity 工程 [ZombiesAreComing](https://github.com/XRFUN-GAME/ZombiesAreComing) 通过 `Build Settings → Android → Export Project` 导出的标准 Gradle 工程，可直接用 Gradle 构建 APK。

- **Unity 版本**：6000.0.49f1
- **导出时间**：2026-11-16
- **包名**：com.xrfun.morigame ｜ **versionName**：1.1.1 ｜ **versionCode**：1
- **目标 ABI**：arm64-v8a ｜ **minSdk**：29 ｜ **targetSdk**：35
- **IL2CPP 状态**：✅ 已预编译（`libil2cpp.so` 在库），构建机不需要 NDK 和 IL2CPP 工具链

## 构建步骤

### 1. 克隆（需要 Git LFS）

本仓库使用 Git LFS 存储大文件（`*.unity3d`），克隆前确认已安装 git-lfs：

```bash
git lfs install          # 每台机器只需执行一次
git clone <本仓库地址>
```

如果克隆后 `unityLibrary/src/main/assets/bin/Data/data.unity3d` 只有几 KB（指针文件），执行 `git lfs pull` 拉取真实文件。

### 2. 配置本机 Android SDK 路径

复制模板生成本机配置：

```bash
cp local.properties.template local.properties
```

编辑 `local.properties`，把 `sdk.dir` 改成**你自己机器**的 Android SDK 路径：

```properties
# Windows 示例（注意反斜杠转义）
sdk.dir=D\:\\Android\\Sdk
# macOS/Linux 示例
sdk.dir=/Users/xxx/Library/Android/sdk
```

### 3. 环境要求

| 组件 | 版本 | 说明 |
| --- | --- | --- |
| JDK | 17 | Gradle 8.11 要求 |
| Gradle | 8.11 | 无 gradlew，用系统 gradle 或 IDE 内置 |
| Android SDK | compileSdk 35 / build-tools 34.0.0 | |
| ~~Android NDK~~ | 不需要 | **IL2CPP 已预编译**（`libil2cpp.so` 已入库），无需 NDK |

> 提示：直接用 Unity Hub 安装 Unity 6000.0.49f1 时勾选 Android Build Support，JDK/SDK/NDK 会全部装好，版本与本工程完全匹配。

### 4. 构建

```bash
gradle assembleRelease
```

输出：`launcher/build/outputs/apk/release/launcher-release.apk`

## 签名说明

当前 `release` 构建使用 **debug 签名**（Unity 导出默认行为）。如需正式签名：

1. 向项目维护者索取 `user.keystore` 及密码（**不要提交进仓库**）
2. 在仓库根目录创建 `keystore.properties`：
   ```properties
   storeFile=user.keystore
   storePassword=你的store密码
   keyAlias=你的alias
   keyPassword=你的key密码
   ```
3. 修改 `launcher/build.gradle`，在 `android { }` 内添加 release 签名配置（可联系维护者提供补丁）

## 常见问题

| 问题 | 解决 |
| --- | --- |
| `SDK location not found` | 没建 `local.properties` 或 `sdk.dir` 路径不对 |
| `data.unity3d` 只有几 KB | 没装/没拉 LFS：`git lfs install && git lfs pull` |
| NDK 相关报错 | 用 SDK Manager 安装 NDK 27.2.12479018 |
| 需要改动游戏内容 | 改 Unity 源工程后重新 Export，**不要直接改本工程** |
