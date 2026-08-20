# MDW — Markdown Website Framework

A lightweight Markdown documentation site framework: renders `.md` / `.mdw` files from the `docs/` directory directly into a responsive website, with built-in navigation tree, theme customization, admin panel, and management API.
<kbd>[简体中文](README.md)</kbd> <kbd>English</kbd>
## Features

- 📝 **Markdown-like syntax** — Supports `.mdw` / `.md`, with extended custom blocks (alerts, cards, external templates)
- 📂 **Automatic routing** — Document directory hierarchy maps to URL structure, with unlimited nesting
- 🧭 **Metadata-driven navigation** — Use `title` / `nav` / `parent` / `order` / `hidden` fields to customize the sidebar
- 🎨 **Theme customization** — `static/style.css` + `static/app.js` are freely modifiable, with dark mode support
- 🔌 **Extension system** — Parser / Renderer / Middleware / custom processors
- 🔄 **Hot reload** — Save a document and the page refreshes immediately (development mode)
- ⚙️ **Admin panel** — `/admin` page to view routes and files
- 🤖 **Management API** — `/api/*` provides route list, page rendering, file upload/download, hot reload, etc. (Bearer Token authentication)

## Quick Start

```bash
pip install -r requirements.txt
python app.py
# Open http://127.0.0.1:8080
```

Create a .mdw file under docs/ and it becomes a corresponding page:

```
docs/
├── index.mdw          → /
├── about.mdw          → /about
└── guide/
    └── styles.mdw     → /guide/styles
```

## Directory Structure

```
MDW/
├── app.py              # Main entry point
├── build.py            # Nuitka packaging script
├── requirements.txt    # Dependencies
├── config/
│   └── site.yaml       # Site configuration (port / title / API Token)
├── docs/               # Documentation source (Markdown / MDW)
├── static/             # Theme assets (style.css / app.js)
├── mdw/                # Core package
└── dist/               # Build artifacts (app.exe / app.bin)
```

## Configuration

`config/site.yaml`（Auto-generated on first run）：

```yaml
host: 0.0.0.0
port: 8080
docs_dir: docs
site_title: MDW
admin_prefix: /admin
auto_reload: true
api:
  enabled: true
  token: mdw-admin        # Management API auth token, please change this
```

## Management API

| Method | Path | Description |
|------|------|------|
| GET | `/api/routes` | Route List |
| GET | `/api/status` | Server Status |
| GET | `/api/page/{path}` | Rendered page HTML (empty path = homepage) |
| GET | `/api/source/{path}` | Download raw documentation file (.md / .mdw) |
| POST | `/api/reload` | Trigger hot reload |
| POST | `/api/upload` | Upload documentation (multipart or JSON) |
| DELETE | `/api/page/{path}` | Delete a document (empty directories are cleaned up cascadingly) |

Authentication: `Authorization: Bearer <token>` or `?token=<token>`.

Full reference available at `docs/api/reference.mdw` (includes curl / Python examples).

## Build Executable

依赖：Nuitka + 对应平台编译器（Windows: MSVC / Linux: gcc+patchelf）。

Before compilation, `docs/` (tutorial), `static/` (theme), and the default `config/` are automatically bundled into the binary (`python build.py --bundle` or automatically during compilation), generating `mdw/_bundle_assets.py`.

```bash
# Windows (produces dist/app.exe, standalone single file, zstd compressed)
python -m nuitka --standalone --onefile --windows-console-mode=force \
  --output-dir=dist \
  --include-data-dir=mdw/templates=mdw/templates \
  --include-package=pygments --include-package=pygments.lexers \
  --include-package=pygments.formatters --include-package=pygments.styles \
  --include-package=yaml --assume-yes-for-downloads app.py

# Linux (produces dist/app.bin; run inside a Linux environment/container)
python -m nuitka --standalone --onefile --output-dir=dist \
  --include-data-dir=mdw/templates=mdw/templates \
  --include-package=pygments --include-package=pygments.lexers \
  --include-package=pygments.formatters --include-package=pygments.styles \
  --include-package=yaml --assume-yes-for-downloads app.py
```

To run the built artifact: execute `app` / `app.exe` directly. **No manual file preparation is required** — on first run, the built-in `docs/` (tutorial), `static/` (theme), and default `config/site.yaml` are extracted to the program's directory; existing user files are not overwritten.

## Documentation

The site includes a complete tutorial (`docs/`):

· `docs/guide/` — 📖 Writing Guide: quick start, syntax basics, metadata, styling, templates, extensions, deployment
· `docs/examples/` — 🧪 Examples: ready-to-use complete examples for all features
· `docs/api/` — 🤖 Management API: interface reference and generic client examples

After starting the site, access it online and read directly; the sidebar auto-generates the navigation tree.

## Related Project

- 📱 **[MDW-Manager](https://github.com/xuexi-meow/MDW-Manager)** — Multi-platform MDW manager (Android / Desktop), visual document management, upload/download, remote control

## Support

If MDW helps you, feel free to buy us a coffee:

![Support QR-WeChat](打赏二维码.png)

*ps:This project is assisted by deepseek-v4*

## License

MIT
