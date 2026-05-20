# myapp — 飞牛 fnOS 应用脚手架

按官方文档 [架构概述](../../docs/core-concepts/framework.md)、[Manifest](../../docs/core-concepts/manifest.md)、[应用入口](../../docs/core-concepts/app-entry.md) 整理的最小目录结构。
开始开发时，建议复制本目录并重命名：

```bash
cp -r apps/myapp apps/<your-appname>
```

## 目录结构

```text
myapp/
├── manifest                 # 应用身份证（appname/version/platform 等）
├── LICENSE                  # 安装前展示的隐私协议（可选）
├── ICON.PNG                 # 64x64 应用中心图标（需补图）
├── ICON_256.PNG             # 256x256 应用详情大图标（需补图）
├── cmd/                     # 生命周期脚本
│   ├── main                 #   start / stop / status（status 运行 0、未运行 3）
│   ├── install_init / install_callback
│   ├── uninstall_init / uninstall_callback
│   ├── upgrade_init / upgrade_callback
│   └── config_init / config_callback
├── config/
│   ├── privilege            # 权限清单
│   └── resource             # 能力声明（如 shares 共享目录）
├── wizard/                  # 用户向导：install / uninstall / upgrade / config
└── app/
    └── ui/
        ├── images/          # 入口图标（建议提供 64 与 256 两个尺寸）
        └── config           # 入口定义（.url 下 key 必须以 appname. 为前缀）
```

## 关键约定速查

- `manifest`
  - `appname` 全局唯一；`version` 格式 `x[.y[.z]][-build]`
  - `platform` 取 `x86` / `arm` / `all`（V1.1.8+，缺省 `x86`，不支持多值）；`arch=x86_64` 已废弃
  - 端口检查：`service_port` + `checkport=true`
  - 依赖：`install_dep_apps=app1>2.2.2:app2`
- `cmd/main`
  - `status` 返回 `0` 代表运行、`3` 代表未运行（其它含义未定义）
  - 错误必须写入 `$TRIM_TEMP_LOGFILE` 后再 `exit 1`，否则前端只会显示「执行XX脚本出错且原因未知」
- `app/ui/config`
  - 入口 key 必须 `appname.xxx` 前缀；`icon` 用 `{0}` 占位（会被替换成 `64` / `256`）
  - `protocol` 留空字符串才是「自适应」；不写默认是 `http`
  - 文件右键入口需 `fileTypes=[...]` + `noDisplay=true`
  - `port` / `url` 支持 `${wizard_xxx}` 动态占位（V1.1.8+）

## 常用环境变量

| 变量 | 含义 |
|---|---|
| `TRIM_APPDEST` | 应用可执行目录（即 `target`） |
| `TRIM_PKGETC` | 静态配置目录（即 `etc`） |
| `TRIM_PKGVAR` | 运行时数据目录（即 `var`） |
| `TRIM_SERVICE_PORT` | 服务端口 |
| `TRIM_USERNAME` | 当前用户名 |
| `TRIM_TEMP_LOGFILE` | 用户可见日志/错误信息写入路径 |

## 打包与上架

- 打包工具：[`fnpack`](../../docs/cli/fnpack.md)
- 应用中心 CLI：[`appcenter-cli`](../../docs/cli/appcentercli.md)
- 流程：[准备工作](../../docs/quick-started/prerequisites.md) → [创建应用](../../docs/quick-started/create-application.md) → [测试应用](../../docs/quick-started/test-application.md) → [上架应用](../../docs/quick-started/publish-application.md)

## 待办

- [ ] 替换 `manifest` 中 `appname` / `display_name` / `desc` 等字段
- [ ] 补 `ICON.PNG` (64x64) 与 `ICON_256.PNG` (256x256)
- [ ] 补 `app/ui/images/icon-64.png` 与 `icon-256.png`
- [ ] 实现 `cmd/main` 的 `start / stop / status` 真实逻辑
- [ ] 根据需要填写 `config/privilege`、`config/resource`
- [ ] 根据需要在 `wizard/` 下添加 `install` / `uninstall` / `upgrade` / `config` 向导
