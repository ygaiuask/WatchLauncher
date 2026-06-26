# WatchLauncher - 安卓手表启动器 (适配 Android 8.1+)

## 项目概述

WatchLauncher 是一款专为安卓智能手表设计的现代桌面启动器应用，基于开源项目 [Forigon](https://github.com/mlm-games/forigon) (GPL-3.0) 进行二次开发。该应用专为运行完整安卓系统（非 Wear OS）的手表设备优化，最低支持 Android 7.0 (API 24)，特别针对 Android 8.1 (API 27) 进行了兼容性适配。

## 原始项目信息

- **原始项目**: Forigon (https://github.com/mlm-games/forigon)
- **开源协议**: GPL-3.0
- **编程语言**: Kotlin
- **UI 框架**: Jetpack Compose + Material 3
- **原始最低 SDK**: API 24 (Android 7.0)
- **架构模式**: MVVM + Koin DI + Navigation3

## 核心功能

### 保留的原始功能
1. **应用抽屉**: 支持列表视图和气泡云视图两种应用浏览方式
2. **虚拟旋转表圈**: 通过边缘手势模拟物理旋转表圈，带触觉反馈
3. **快速设置面板**: 手电筒、勿扰模式、WiFi、蓝牙、亮度调节
4. **模糊搜索**: 支持拼音/中文模糊匹配的应用快速查找
5. **应用隐藏**: 可隐藏不需要在主屏显示的应用
6. **可配置手势**: 长按或双击触发不同操作
7. **动态模式**: 低功耗设备的运动减少模式
8. **UI 缩放**: 适配不同尺寸的小屏幕
9. **D-pad 和键盘导航**: 支持物理按键操作
10. **圆形和方形屏幕**: 自适应不同屏幕形状

### 新增/定制化功能
1. **Android 8.1 完整适配**: 移除了 API 28+ 专属特性，确保在老旧手表设备上稳定运行
2. **中文界面优化**: 完善的中文本地化翻译，符合中国用户使用习惯
3. **精简资源占用**: 移除高版本 API 特有的资源配置，减小 APK 体积
4. **兼容性增强**: 添加 `tools:targetApi="o_mr1"` 标记，确保构建工具正确处理 API 差异

## 修改内容详解

### 1. AndroidManifest.xml 修改

```xml
<!-- 移除高版本 API 特性 -->
- android:dataExtractionRules="@xml/data_extraction_rules"  <!-- API 31+ -->
- android:localeConfig="@xml/locales_config"                 <!-- API 33+ -->
- android:enableOnBackInvokedCallback="true"                 <!-- API 33+ -->
- android:resumeWhilePausing="true"                          <!-- API 28+ -->

<!-- 新增 -->
+ tools:targetApi="o_mr1"  <!-- 标记目标 API 级别为 Android 8.1 -->
```

**原因**: 以上属性在 Android 8.1 (API 27) 上不可用，移除后避免潜在的运行时异常。

### 2. build.gradle.kts 修改

```kotlin
// 目标 SDK 版本调整
- targetSdk = 36
+ targetSdk = 27

// 版本标识
- versionName = "v1.1.3"
+ versionName = "v1.1.3-watch"

// 移除高版本 AGP 特性
- androidResources {
-     localeFilters += setOf(...)  // 需要 AGP 8+ 且目标 API 33+
- }

// 签名配置增加默认值（用于开发构建）
- storePassword = System.getenv("STORE_PASSWORD")
+ storePassword = System.getenv("STORE_PASSWORD") ?: "android"
```

**原因**: 
- `targetSdk = 27` 确保应用行为与 Android 8.1 系统最佳匹配
- `localeFilters` 在低版本目标 SDK 上不适用
- 签名配置默认值方便开发构建

### 3. settings.gradle.kts 修改

```kotlin
- rootProject.name = "TvLauncher"
+ rootProject.name = "WatchLauncher"
```

### 4. 应用名称修改 (strings.xml)

```xml
- <string name="app_name">Forigon</string>
+ <string name="app_name">WatchLauncher</string>
```

### 5. Gradle 配置调整 (gradle-wrapper.properties)

```properties
- networkTimeout=10000
+ networkTimeout=120000  # 增加网络超时，适应慢速网络环境
```

## 兼容性说明

### API 级别兼容性矩阵

| 特性 | 最低 API | Android 8.1 (27) |
|------|---------|-------------------|
| 基础启动器功能 | 24 | 完全支持 |
| Material 3 界面 | 24 | 完全支持 |
| 应用抽屉 | 24 | 完全支持 |
| 虚拟表圈 | 24 | 完全支持 |
| 快速设置 | 24 | 完全支持 |
| 双击锁屏 | 28 | 不支持 (已注释) |
| 隐私空间 | 35 | 不支持 (运行时禁用) |
| 区域配置 | 33 | 不支持 (已移除) |

### Android 8.1 特别注意事项

1. **通知权限**: Android 8.1 使用通知渠道 (Notification Channels)，但启动器应用不涉及通知
2. **后台限制**: Android 8.0+ 对后台服务有严格限制，本应用作为前台启动器不受影响
3. **自适应图标**: Android 8.0 引入自适应图标，应用已包含相应资源

## 构建指南

### 环境要求

| 组件 | 版本要求 |
|------|---------|
| JDK | 17 或 21 (推荐 Zulu/Adoptium JDK 21) |
| Gradle | 9.2.1 (wrapper 自动下载) |
| Android SDK | API 36 (编译) + API 27 (目标) |
| Android Build Tools | 36.0.0+ |
| Kotlin | 2.3.10 |

### 构建步骤

#### 方式一：使用 Android Studio（推荐）

1. 安装 Android Studio (Koala 2024.1.2+)
2. 通过 SDK Manager 安装:
   - Android SDK Platform 36
   - Android SDK Platform 27
   - Android SDK Build-Tools 36.0.0
3. 克隆或解压项目到本地
4. 用 Android Studio 打开项目目录
5. 等待 Gradle 同步完成
6. 选择 `Build > Build APK(s)` 或 `Build > Generate Signed Bundle / APK`

#### 方式二：命令行构建

```bash
# 设置环境变量
export ANDROID_HOME=/path/to/android/sdk
export JAVA_HOME=/path/to/jdk21

# 清理构建
./gradlew clean

# 编译 Debug APK
./gradlew assembleDebug

# 编译 Release APK（需要签名配置）
./gradlew assembleRelease

# APK 输出路径
# app/build/outputs/apk/debug/app-debug.apk
# app/build/outputs/apk/release/app-release.apk
```

#### 在 Windows 上构建

```powershell
# 设置环境变量
$env:ANDROID_HOME = "C:\YourAndroidSdkPath"
$env:JAVA_HOME = "C:\Program Files\Java\jdk-21"

# 执行构建
.\gradlew.bat assembleDebug
```

### 签名配置

Release 构建需要签名密钥。可通过以下方式配置：

1. **环境变量方式**（推荐用于 CI/CD）:
```bash
export KEYSTORE_PATH=/path/to/keystore.jks
export STORE_PASSWORD=your_password
export KEY_ALIAS=your_alias
export KEY_PASSWORD=your_key_password
```

2. **默认配置**: 在项目根目录放置 `release.keystore` 文件，使用默认密码 `android`，别名为 `watchlauncher`。

3. **生成调试密钥**:
```bash
keytool -genkey -v -keystore release.keystore -alias watchlauncher \
  -keyalg RSA -keysize 2048 -validity 10000 \
  -storepass android -keypass android \
  -dname "CN=WatchLauncher, OU=Dev, O=Watch, L=City, S=State, C=CN"
```

## 项目结构

```
WatchLauncher/
├── app/
│   ├── src/main/
│   │   ├── java/app/forigon/
│   │   │   ├── data/           # 数据模型和仓库
│   │   │   │   ├── repository/ # AppRepository
│   │   │   │   ├── AppModel.kt
│   │   │   │   ├── Constants.kt
│   │   │   │   └── ...
│   │   │   ├── di/             # 依赖注入 (Koin)
│   │   │   │   └── AppModule.kt
│   │   │   ├── helper/         # 工具类
│   │   │   │   ├── iconpack/
│   │   │   │   ├── BitmapUtils.kt
│   │   │   │   ├── PermissionManager.kt
│   │   │   │   └── ...
│   │   │   ├── platform/       # 平台特定代码
│   │   │   ├── settings/       # 设置管理
│   │   │   │   ├── LauncherSettings.kt
│   │   │   │   └── ...
│   │   │   ├── ui/             # UI 层
│   │   │   │   ├── components/ # 可复用组件
│   │   │   │   ├── screens/    # 页面
│   │   │   │   │   ├── ControlCenter.kt
│   │   │   │   │   ├── SettingsScreen.kt
│   │   │   │   │   ├── WatchAppDrawer.kt
│   │   │   │   │   └── WatchFaceScreen.kt
│   │   │   │   ├── theme/      # 主题
│   │   │   │   └── LauncherShell.kt
│   │   │   ├── LauncherApp.kt
│   │   │   ├── LauncherViewModel.kt
│   │   │   └── MainActivity.kt
│   │   ├── res/                # 资源文件
│   │   │   ├── values/         # 英文默认字符串
│   │   │   ├── values-zh/      # 中文字符串
│   │   │   └── ...
│   │   └── AndroidManifest.xml
│   └── build.gradle.kts
├── gradle/
│   ├── wrapper/
│   └── libs.versions.toml     # 版本目录（依赖管理）
├── build.gradle.kts            # 根构建文件
├── settings.gradle.kts
├── gradle.properties
├── gradlew / gradlew.bat
└── MODIFICATIONS.md            # 本文档
```

## 依赖版本

| 依赖 | 版本 |
|------|------|
| Kotlin | 2.3.10 |
| Android Gradle Plugin (AGP) | 9.0.0 |
| Compose BOM | 2026.02.00 |
| Material 3 | 1.4.0 |
| Koin | 4.1.1 |
| Navigation3 | 1.0.1 |
| DataStore Preferences | 1.2.0 |

## 许可证

本项目基于 GPL-3.0 许可证开源。原始项目 Forigon 版权归 mlm-games 所有。

修改部分同样遵循 GPL-3.0 协议发布。

## 常见问题

### Q: 为什么选择 Forigon 作为基础项目？
A: Forigon 是目前 GitHub 上最活跃的开源安卓手表启动器之一，具有以下优势：
- 专门的安卓手表适配（支持完整安卓和 Wear OS）
- 现代 Material 3 UI 设计
- 最低 API 24（兼容 Android 7.0+）
- 纯 Kotlin + Compose 技术栈
- GPL-3.0 开源协议，允许修改和分发
- 活跃维护（2025-2026 年持续更新）

### Q: 如何在 Android 8.1 手表上安装？
A: 可以通过以下方式安装：
1. ADB 安装: `adb install watchlauncher.apk`
2. 将 APK 文件传输到手表存储中，使用文件管理器点击安装
3. 通过应用商店分发

### Q: 安装后如何设置为默认启动器？
A: 
1. 安装 APK 后，按手表的 Home 键
2. 系统会弹出"选择主屏幕应用"对话框
3. 选择 "WatchLauncher" 并点击"始终"
4. 如需更改，前往 系统设置 > 应用 > 默认应用 > 主屏幕应用

### Q: 与原版 Forigon 有什么区别？
A: 主要区别：
1. 适配 Android 8.1 API 级别
2. 移除高版本 API 依赖
3. 优化中文语言支持
4. 调整目标 SDK 版本以匹配老旧设备
5. 简化构建配置，降低开发环境要求

## 技术支持

- 原始项目 Issues: https://github.com/mlm-games/forigon/issues
- GPL-3.0 协议全文: https://www.gnu.org/licenses/gpl-3.0.html
