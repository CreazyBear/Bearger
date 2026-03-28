# Bearger 项目说明（供 Agent / 协作者）

## 仓库性质

本仓库**不是**应用源码工程（无 Xcode / Android 工程），而是一个 **Jekyll 静态站点**，用于托管独立应用相关的**隐私政策**与**技术支持**文档。站点标题在 `index.md` 中为 **「跬步」**，页脚标注 *Just For Fun —— Bearger*。

## 技术栈与配置

- **静态生成**：Jekyll
- **主题**：`jekyll-theme-cayman`（见 `_config.yml`）
- **入口**：根目录 `index.md`

## 目录结构

| 路径 | 说明 |
|------|------|
| `index.md` | 首页，汇总各产品文档入口 |
| `_config.yml` | Jekyll 配置 |
| `BitClip/` | macOS 剪贴板类应用文档（原名 Clipber）；含 `PrivacyPolicy.md`、`TechSupport.md`，另有 `BitClip开发总结.md`、`更新日志.md` 等开发笔记 |
| `NFCer/` | iOS NFC 工具文档：`PrivacyPolicy.md`、`TechSupport.md` |
| `FJScanner/` | 摄像头数字化/扫码类应用文档：`PrivacyPolicy.md`、`TechSupport.md` |

## 产品概要（来自文档）

1. **BitClip**：轻量剪贴板扩展（菜单栏入口），文本/图片历史、截图与编辑、搜索等；开发笔记提及 Objective-C、Storyboard/XIB、FMDB、CocoaPods。**应用源码不在本仓库。**（曾用名 Clipber）
2. **NFCer**：轻量 NFC 工具（读/写标签等），强调免费、无广告。
3. **FJScanner**：用摄像头将现实数据数字化；强调轻量、免费、无广告。

技术支持文档中的反馈邮箱多为 `bearger@qq.com`。

## 线上地址（GitHub Pages）

根据 `origin` 远程 `git@github.com:CreazyBear/Bearger.git`，在 **未使用自定义域名**、且按 GitHub **项目站点**默认规则部署时，典型 URL 为：

**https://CreazyBear.github.io/Bearger/**

生效条件：仓库 **Settings → Pages** 已开启并指定发布分支；若改名仓库/用户或配置自定义域名（如添加 `CNAME`），实际 URL 会变化。是否已成功部署需以 GitHub Pages 设置或访问该链接为准。

## Git 远程（参考）

- `origin`：`CreazyBear/Bearger`

---

*本文档由对话结论整理，用于后续 Agent 或人工快速了解仓库用途与线上入口。*
