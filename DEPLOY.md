# DEPLOY.md — amazon-decision-tool

## 部署信息

| 项目 | 值 |
|---|---|
| Vercel 项目 | `amazon-decision-tool`（team: `zbjdsbjs-projects` / `team_fMQxP2TOmuuYYy7HSrEoqcDX`） |
| **生产域名** | **https://amazon-decision-tool.vercel.app** |
| 本次部署 ID | `dpl_a6PJFRLLtPBPDSXi5J5U2wE9KSqU` |
| GitHub 仓库 | https://github.com/ZBJDSBJ/amazon-decision-tool （public） |
| 部署区域 | `sin1`（新加坡，来自 `vercel.json`） |
| 部署时间 | 2026-09-11 |
| 部署状态 | `READY` / `target=production` |
| 源文件 | `D:/PI/amazon-product-research-tool/mvp/landing/index.html` |

## 仓库内容

| 文件 | 说明 |
|---|---|
| `landing.html` | 落地页正文（真正的页面） |
| `index.html` | 入口重定向页 → `landing.html` |
| `demo-report.html` | 由 `tools/make_report.py` 真实生成的示例报告（HTML 自包含） |
| `favicon.svg` / `favicon.ico` / `apple-touch-icon.png` / `icon-192.png` / `icon-512.png` / `site.webmanifest` | 站点图标全套（由 `tools/make_favicon.py` 生成） |
| `vercel.json` | 静态站点配置（无构建，区域 sin1，缓存与安全头） |
| `README.md` | 项目说明 |
| `.gitignore` | 排除 `.vercel/`、`.env` 等 |

---

## ⚠️ 上线前必须先处理的两件事

1. **占位邮箱**：页面里的 `hello@example.com` 是占位符，**必须换成真实收件地址**，否则访客点「发数据给我」会发到一个不存在的邮箱。
2. **`noindex`**：当前 `<meta name="robots" content="noindex, nofollow">` 是我刻意加的，防止这个占位符版本被搜索引擎收录。**页面正式对外时再删掉**（删掉后搜索引擎才可能收录）。

---

## 本机网络限制（这是本次部署最需要知道的事）

| 目标 | 可达性 |
|---|---|
| `github.com:443`（git push 用的主机） | ❌ **21 秒超时，完全不可达** |
| `api.github.com` | ✅ 通（所以 `gh` 可用） |
| `*.vercel.app`（部署出来的页面） | ❌ **超时** |
| `api.vercel.com` | ✅ 通（所以 Vercel CLI 可用） |

**三个后果**：

1. **`git push` 用不了** → 仓库内容改用 **GitHub Contents API** 写入（5 个文件各一个 commit）。
2. **本机无法用 curl 验证页面渲染** → 本次验证是查 **Vercel API 的部署产物**（`readyState=READY`、构建日志 `Deployment completed`、文件清单含全部 4 个文件），**不是**真的把页面抓下来看过。浏览器如果走代理/VPN，应该能正常打开。
3. **本地 git 历史与远程不一致**：本地是 1 个 commit（`Initial commit: 完整部署包`），远程是 API 创建的 5 个 commit。等 `github.com` 可达后执行一次：

```bash
git fetch origin && git reset --hard origin/main
```

---

## 更新流程

**方式 A（当前环境下唯一可用）—— 直接用 Vercel CLI 直传：**

```bash
# 从 1 份 HTML 重新生成部署包（在 vercel-auto-deploy 技能目录下执行）
python scripts/generate_project.py \
  --html "D:/PI/amazon-product-research-tool/mvp/landing/landing.html" \
  --project-name amazon-decision-tool \
  --output-dir "D:/PI/amazon-decision-tool" \
  --github-user ZBJDSBJ

cd /d/PI/amazon-decision-tool
export VERCEL_TOKEN="<从 config/credentials.json 读取，勿写入本文件>"
vercel deploy --prod --yes --scope team_fMQxP2TOmuuYYy7HSrEoqcDX
```

**方式 B（`github.com` 可达后）—— push 触发自动部署：**

前提：在浏览器里把 **Vercel GitHub App 安装到该仓库**（本次 `vercel link` 报 `Failed to connect ZBJDSBJ/amazon-decision-tool to project`，就是这个原因）。装好之后：

```bash
git add . && git commit -m "更新落地页" && git push origin main
# Vercel 会自动部署，30-60 秒后生效
```

---

## 用到的凭证（不在此文件中）

- Vercel Token：存在 `D:\AIWorks\Skills\my-skills\vercel-auto-deploy\config\credentials.json`（按 GitHub 账号 `ZBJDSBJ` 分组，该目录已被 `.gitignore` 排除）
- GitHub：`gh` CLI 已登录（keyring）
