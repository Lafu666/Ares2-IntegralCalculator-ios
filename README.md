# 阿瑞斯2积分计算器 - iOS IPA

将网页 `https://sqiyue-github-io.pages.dev` 离线打包成 iOS 应用，支持 iOS 15 - 18，通过 GitHub Actions 自动构建。

## 特性

- **完全离线运行** — HTML/CSS/JS 全部打包进 App，无需联网，启动零延迟
- **iOS 15-18 兼容** — 部署目标 iOS 15.0，覆盖 iOS 15、16、17、18
- **GitHub Actions 自动构建** — 推送代码即自动产出 IPA
- **原生体验** — WKWebView，无浏览器 UI，安全区适配，禁用多余手势
- **小体积** — Swift + UIKit，无 SwiftUI 依赖，无第三方库

## 使用方法

### 1. 推送到 GitHub

```bash
cd ipa-builder
git init
git add .
git commit -m "init: 阿瑞斯2积分计算器 iOS wrapper"
git branch -M main
git remote add origin https://github.com/<你的用户名>/<仓库名>.git
git push -u origin main
```

推上去后，GitHub Actions 会自动触发构建（约 5-10 分钟）。

### 2. 下载 IPA

1. 打开仓库页面 → **Actions** 标签
2. 选择最新一次 `Build IPA` 运行
3. 在 Artifacts 区域下载 `AresPointsCalculator-iOS15-18`
4. 解压得到 `AresPointsCalculator.ipa`

### 3. 安装到 iPhone

IPA 是**未签名**的，需要用下面任一工具签名后安装：

| 工具 | 适用系统 | 说明 |
|------|---------|------|
| **Sideloadly** | iOS 15-18 | Windows/macOS，免费 Apple ID 即可，需电脑 |
| **AltStore / SideStore** | iOS 15-18 | 自动续签，AltStore 需电脑，SideStore 可独立运行 |
| **TrollStore** | iOS 14-16.6.1 / 17.0 | 永久签名，需对应系统版本 |
| **爱思助手** | iOS 15-18 | 国内工具，操作简单 |

**Sideloadly 步骤**（推荐）：
1. 下载 https://sideloadly.io/
2. iPhone 用数据线连到电脑，信任电脑
3. 拖入 `AresPointsCalculator.ipa`
4. 输入 Apple ID 邮箱和密码（仅用于生成免费证书）
5. 点 Start，完成后 iPhone 桌面出现 App
6. iPhone 设置 → 通用 → VPN 与设备管理 → 信任开发者

## 重新触发构建

- **自动**：推送到 `main` 分支会自动构建
- **手动**：在 Actions 页选 `Build IPA` → Run workflow
- **发版**：打 tag `git tag v1.0.0 && git push origin v1.0.0` 会自动创建 Release 并附带 IPA

## 本地生成 Xcode 工程（可选）

```bash
brew install xcodegen
cd ipa-builder
xcodegen generate
open AresPointsCalculator.xcodeproj
```

## 项目结构

```
ipa-builder/
├── .github/workflows/build.yml   # GitHub Actions 构建流程
├── App/
│   ├── Sources/
│   │   ├── AppDelegate.swift     # App 入口
│   │   ├── SceneDelegate.swift    # 多场景支持
│   │   └── WebViewController.swift # WKWebView 控制器
│   ├── Resources/
│   │   ├── Assets.xcassets/       # App 图标
│   │   ├── Web/index.html         # 离线打包的网页
│   │   └── Info.plist             # App 配置
├── project.yml                    # xcodegen 工程定义
└── README.md
```

## 更新网页内容

替换 `App/Resources/Web/index.html`，提交推送即可重新构建。

## 技术细节

- **部署目标**：iOS 15.0（支持 iPhone 6s 及以上机型）
- **架构**：arm64（64位，覆盖所有 iOS 15+ 设备）
- **签名方式**：未签名导出，支持任意 Apple ID 自签名
- **App Bundle ID**：`com.sqiyue.arespoints`
- **显示名**：阿瑞斯2积分
- **方向**：竖屏锁定（与原网页 viewport 一致）

## 故障排查

**Build failed: Code signing is required**
确保 workflow 用的是 `CODE_SIGNING_ALLOWED=NO`，已禁用签名。

**下载的 IPA 装不上**
iPhone 设置 → 通用 → VPN 与设备管理 → 信任你的开发者证书。

**App 闪退**
检查 iOS 版本是否 ≥ 15.0；32 位设备（iPhone 5s 以下）不支持。

**GitHub Actions 失败**
查看 Actions → 失败的运行 → build-logs artifact 里的 `build-archive.log`。
