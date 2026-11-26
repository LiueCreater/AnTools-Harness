# AnTools Harness

AnTools Harness 是一个安卓「工具载体」：本体只提供插件运行环境、权限与外观框架，具体功能由插件（ZIP）提供。包名 `com.antools.harness.liue`，minSdk 24 / target 34。

本仓库归档从 `v1demo1` 到 `v1.0demo7rebuild2` 的全部版本：每个版本一个 tag、一个 Release。Release 正文使用当时的交付说明，附件包含该版本的 **APK** 与 **源码 ZIP**。推送新 tag 时由 GitHub Actions 自动创建 Release。

> **外观插件终结版本**：`v1.0demo6` 是最后一个完整支持外部外观插件（`type: appearance`）的版本。`v1.0demo7` 起外观设置收回宿主端，读取旧外观插件不会报错，但会在「全部插件」页标注「已弃用」，其渲染外观可能与新宿主不兼容。

## 版本列表

| Tag | 主要改动 | Release |
|---|---|---|
| `v1demo1` | 新增骨架 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1demo1) |
| `v1.0demo2` | 导出位置、插件设置 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo2) |
| `v1.0demo3` | 换肤更新、包名冲突、外观接口 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo3) |
| `v1demo3fix1` | 三处修复（Manifest / 去重 / 皮肤残留） | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1demo3fix1) |
| `v1.0demo4` | 整合包、排序、导出多选 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo4) |
| `v1.0demo5` | 图标与分组 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo5) |
| `v1.0demo5fix1` | 排序重复、主页双列、视频接口预留 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo5fix1) |
| `v1.0demo5fix2` | 全模块 SDK、风险权限提醒（versionCode 9） | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo5fix2) |
| `v1.0demo5fix3` | 二进制原语、Intent 白名单、插件自建管线 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo5fix3) |
| `v1.0demo5fix4` | 多选/第三方选文件、分享收件箱 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo5fix4) |
| `v1.0demo6` | 主题渲染修复、三种玻璃、连续曲率，最后一个支持外观插件 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo6) |
| `v1.0demo7` | 外观设置收回宿主、背景图/透明度、三色取色、深色灰阶，旧外观插件标弃用 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo7) |
| `v1.0demo7rebuild1` | 壁纸改为独立底色+媒体层，0% 完整图片、100% 只剩底色，去掉 20% 遮罩 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo7rebuild1) |
| `v1.0demo7rebuild2` | 深浅主题各一套壁纸、竖屏/横屏裁剪、切换主题与横竖屏自动匹配，外观设置改为保存制 | [下载](https://github.com/LiueCreater/AnTools-Harness/releases/tag/v1.0demo7rebuild2) |

说明：

- `v1demo1`、`v1demo3fix1` 是当时实际交付的命名，保留原样，没有改写成 `v1.0demo1` / `v1.0demo3fix1`。
- `v1.0demo5fix2` 有两次构建（主题颜色 versionCode 8、权限模块 versionCode 9）。这里归档最终版 versionCode 9，交付说明中写明以它为准。
- `v1.0demo6` 是外观插件最后支持版本；`v1.0demo7` 起外观设置由宿主提供，旧外观插件仅保留兼容读取并标注「已弃用」。
- 所有 APK 使用同一签名，可直接覆盖升级；源码 ZIP 内**不含** keystore。

## 安装

1. 在对应 Release 下载 `AnTools Harness_*.apk`。
2. 允许安装未知来源应用后安装。
3. 首次打开是空主页，从「全部插件 → 安装插件」装本地 ZIP、GitHub 插件或内置示例。
4. 覆盖安装旧版本不需要卸载，插件、排序与设置会保留。

## 仓库结构

```
.
├── README.md
├── versions.tsv                    # 版本发布清单：tag、APK/源码/交付说明地址
└── .github/
    ├── bootstrap-trigger           # 历史版本初始化标记（修改它会重跑初始化）
    └── workflows/
        ├── release-on-tag.yml      # 推 tag 自动建 Release
        └── bootstrap-releases.yml  # 为历史版本补 tag + Release（幂等）
```

仓库不直接存放 APK/源码二进制：`versions.tsv` 记录归档地址，工作流在 runner 下载、`unzip -t` 校验后上传到 GitHub Release，之后 Release 资产独立存在，不再依赖原地址。

## 发布机制

- **推 tag 自动发布**：`release-on-tag.yml` 监听 `v*` tag。推送后读取 `versions.tsv` 对应行，下载 APK、源码 ZIP 和交付说明，校验 ZIP 完整性，再用交付说明作为正文创建 Release，附件带 APK 与源码。
- **历史版本初始化**：`bootstrap-releases.yml` 在 `bootstrap-trigger` 变化时运行，为 `versions.tsv` 中所有 tag 补建 tag 与 Release；已存在的 Release 会跳过，可重复执行。
- **新增版本**：在 `versions.tsv` 加一行（tag、APK 名/地址、源码名/地址、交付说明地址），再 `git push origin <新tag>`，不需要改工作流。

## 插件开发

- 最新插件规范与教程在 `v1.0demo7rebuild2` 源码 ZIP 的 `docs/` 目录中。
- 插件包是 ZIP：根目录 `basic.txt`（或 `basis.txt`）+ `README.md` + 入口页；支持图标、权限声明、整合包、分享接收（`accept`）。
- 插件运行在宿主 WebView 内，所有系统能力经宿主桥按 `permissions` 校验；插件以用户身份运行，安装时会提示风险权限。
- **外观插件已弃用**：`v1.0demo6` 是最后一个支持外部外观插件的版本；`v1.0demo7` 起请使用宿主「外观设置」，不要再新建 `type: appearance` 插件。

## 从源码构建

源码 ZIP 解压后：

```bash
cd AnToolsHarness
./build.sh v1.0demo7rebuild2 15
# 产物 build/final.apk
```

依赖 JDK 17、Android SDK platform 34、build-tools 34.0.0（aapt2 / d8 / apksigner / zipalign），不依赖 Gradle。签名需自备 keystore；仓库不提供私钥。

## 安全说明

- 插件与宿主同进程，权限由宿主 API 层拦截，不是操作系统级沙箱。
- 高风险权限（读文件、导出、联网、剪贴板、通知、分享、外部打开、读取视频）会在安装和详情页提示。
- 只安装你信任来源的插件。

## 许可

仓库内的代码与文档用于 AnTools Harness 的开发与分发；第三方插件版权归各自作者。
