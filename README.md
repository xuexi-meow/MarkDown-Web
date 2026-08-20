# MDW — Markdown Website Framework

轻量级 Markdown 文档建站框架：把 `docs/` 目录下的 `.md` / `.mdw` 文件直接渲染为响应式网站，内置导航树、主题定制、管理后台与管理 API。

<kbd>简体中文</kbd> <kbd>[English](README-en.md)</kbd>

## 特性

- 📝 **类 Markdown 语法** — 支持 `.mdw` / `.md`，扩展自定义块（提示框、卡片、外部模板）
- 📂 **自动路由** — 文档目录层级即 URL 结构，嵌套无上限
- 🧭 **元数据驱动导航** — `title` / `nav` / `parent` / `order` / `hidden` 等字段定制侧边栏
- 🎨 **主题定制** — `static/style.css` + `static/app.js` 可自由修改，支持暗色模式
- 🔌 **扩展系统** — Parser / Renderer / Middleware / 自定义处理器
- 🔄 **热重载** — 保存文档即刷新页面（开发模式）
- ⚙️ **管理后台** — `/admin` 页面查看路由与文件
- 🤖 **管理 API** — `/api/*` 提供路由列表、页面渲染、文件上传下载、热重载等接口（Bearer Token 鉴权）

## 快速开始

```bash
pip install -r requirements.txt
python app.py
# 打开 http://127.0.0.1:8080
```

在 `docs/` 下创建 `.mdw` 文件即生成对应页面：

```
docs/
├── index.mdw          → /
├── about.mdw          → /about
└── guide/
    └── styles.mdw     → /guide/styles
```

## 目录结构

```
MDW/
├── app.py              # 主入口
├── build.py            # Nuitka 打包脚本
├── requirements.txt    # 依赖
├── config/
│   └── site.yaml       # 站点配置（端口/标题/API Token）
├── docs/               # 文档源（Markdown/MDW）
├── static/             # 主题资源（style.css / app.js）
├── mdw/                # 核心包
└── dist/               # 编译产物（app.exe / app.bin）
```

## 配置

`config/site.yaml`（首次运行自动生成）：

```yaml
host: 0.0.0.0
port: 8080
docs_dir: docs
site_title: MDW
admin_prefix: /admin
auto_reload: true
api:
  enabled: true
  token: mdw-admin        # 管理 API 鉴权 Token，请修改
```

## 管理 API

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/routes` | 路由列表 |
| GET | `/api/status` | 服务器状态 |
| GET | `/api/page/{path}` | 页面渲染 HTML（空路径=首页） |
| GET | `/api/source/{path}` | 下载原始文档文件（.md/.mdw） |
| POST | `/api/reload` | 触发热重载 |
| POST | `/api/upload` | 上传文档（multipart 或 JSON） |
| DELETE | `/api/page/{path}` | 删除文档（空目录自动级联清理） |

鉴权：`Authorization: Bearer <token>` 或 `?token=<token>`。

完整参考见 `docs/api/reference.mdw`（含 curl / Python 通用示例）。

## 编译可执行文件

依赖：Nuitka + 对应平台编译器（Windows: MSVC / Linux: gcc+patchelf）。

编译前会自动把 `docs/`（教程）、`static/`（主题）、默认 `config/` 打包进二进制
（`python build.py --bundle` 或编译时自动执行），生成 `mdw/_bundle_assets.py`。

```bash
# Windows（产出 dist/app.exe，单文件，zstd 压缩）
python -m nuitka --standalone --onefile --windows-console-mode=force \
  --output-dir=dist \
  --include-data-dir=mdw/templates=mdw/templates \
  --include-package=pygments --include-package=pygments.lexers \
  --include-package=pygments.formatters --include-package=pygments.styles \
  --include-package=yaml --assume-yes-for-downloads app.py

# Linux（产出 dist/app.bin；在 Linux 环境/容器中执行）
python -m nuitka --standalone --onefile --output-dir=dist \
  --include-data-dir=mdw/templates=mdw/templates \
  --include-package=pygments --include-package=pygments.lexers \
  --include-package=pygments.formatters --include-package=pygments.styles \
  --include-package=yaml --assume-yes-for-downloads app.py
```

运行编译产物：直接执行 `app` / `app.exe` 即可。**无需手动准备任何文件**——
首次运行会自动把内置的 `docs/`（教程文档）、`static/`（主题样式）和默认
`config/site.yaml` 解压到程序同目录；用户已有内容不会被覆盖。

## 文档

站点内置完整教程（`docs/`）：

- `docs/guide/` — 📖 编写指南：快速开始、语法基础、元数据、样式、模板、扩展、部署
- `docs/examples/` — 🧪 示例集：各功能开箱即用的完整示例
- `docs/api/` — 🤖 管理 API：接口参考与通用客户端示例

启动后访问站点即在线阅读，侧边栏自动生成导航树。

## 相关项目

- 📱 **[MDW-Manager](https://github.com/xuexi-meow/MDW-Manager)** — 多平台 MDW 管理器（Android / 桌面端），可视化管理文档、上传下载、远程控制

## 支持

如果 MDW 对你有帮助，欢迎打赏支持：

![打赏二维码](打赏二维码.png)

*ps:本项目由deepseek-v4辅助完成*

## 许可

MIT
