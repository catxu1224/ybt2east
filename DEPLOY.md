# YBT → EAST 血缘站点 · GitHub + Zeabur 静态部署操作手册

| 项目 | 说明 |
| --- | --- |
| 交付物 | 15 个静态 HTML 页面（`index.html` 为入口），无构建步骤、无后端依赖 |
| 目标 | 通过 GitHub 托管源码，由 Zeabur 以静态站点方式对外发布，自动分配 HTTPS 域名 |
| 适用对象 | 无需前端/运维经验；熟悉命令行者可直接走命令行路径 |
| 预计耗时 | 首次全流程约 20–30 分钟（其中 Zeabur 首次构建 1–3 分钟） |
| 手册版本 | 2026-10-14 |

---

## 0. 一分钟速览

| 步骤 | 做什么 | 产出 |
| --- | --- | --- |
| ① 整理文件 | 只保留 15 个 HTML + `.gitignore`，排除 `.workbuddy/` | 干净的可发布目录 |
| ② 上传 GitHub | 新建仓库并上传（网页拖拽 或 Git 命令行二选一） | 仓库地址 `https://github.com/<账号>/<仓库>` |
| ③ Zeabur 部署 | 登录 → 建项目 → Create Service → 选 Git 仓库 | 构建成功、服务运行中 |
| ④ 绑定域名 | Domains 页签 → Generate Domain | `https://<名称>.zeabur.app`（自动 HTTPS） |
| ⑤ 上线验收 | 按第 6 节清单逐项点检 | 可对外访问的站点 |

> **重要提醒（发布前必读）**：Zeabur 会把仓库里的**所有文件**当作静态资源对外提供，任何人可直接访问与下载。本项目目录中的 `.workbuddy/` 含原始备份与工作记录，**严禁上传**。若内容涉及行内报送底稿等敏感信息，请将 GitHub 仓库设为 **Private**（Zeabur 支持私有仓库部署）。

---

## 1. 前置条件

| 项 | 要求 | 本机现状 |
| --- | --- | --- |
| GitHub 账号 | 用于存放源码（免费注册） | 需自备 |
| Zeabur 账号 | 用 GitHub 账号一键登录（免费套餐可用） | 需自备 |
| Git（可选） | 仅在走命令行上传时需要 | 本机已装 git 2.55.0 |
| 浏览器 | Chrome / Edge 即可 | — |

### 发布内容清单

| 类别 | 文件 | 处理方式 |
| --- | --- | --- |
| 必传（入口） | `index.html` | 上传 |
| 必传（主题页） | `00-Overview.html`、`01-PublicInfo.html` ~ `12-Other.html` | 上传（共 13 个） |
| 必传（单页完整版） | `YBT2EAST-FullLineage.html` | 上传 |
| 必传（配置） | `.gitignore` | 上传（已生成，用于排除内部目录） |
| 可选（文档） | `DEPLOY.md`（本手册） | 建议上传，便于后续维护 |
| **禁止上传** | `.workbuddy/`（约 5.9 MB，含备份与工作记录） | 由 `.gitignore` 自动排除 |

合计发布体积约 5.7 MB，其中 `00-Overview.html`（1.7 MB）与 `YBT2EAST-FullLineage.html`（1.7 MB）较大，属正常范围。

### 已完成的发布前自检（本次已代执行）

| 检查项 | 结果 |
| --- | --- |
| 内部链接是否死链 | 无死链（13 个互相引用页面全部命中） |
| 链接与实际文件名**大小写**是否一致 | 一致（Windows → Linux 迁移最常见的 404 诱因，已排除） |
| 是否存在本地绝对路径 / `file://` 引用 | 无（全部为相对路径，可跨域托管） |
| 编码 | 全部 UTF-8，中文不会乱码 |
| 内联 JS 语法 | 15 个页面全部通过编译检查 |

> 补充说明：Zeabur 使用 Caddy 托管静态页面，仓库中若存在 `.git`、`node_modules`、`.venv`、`vendor` 路径会被平台屏蔽返回 404，本项目不涉及。

---

## 2. 步骤一：整理待发布目录

1. 打开目录 `D:\FTP_Share\ybt2east`。
2. 确认存在 `.gitignore`（本手册已生成），其内容用于排除 `.workbuddy/`：

```
.workbuddy/
Thumbs.db
Desktop.ini
.DS_Store
*.swp
*~
*.tmp
*.bak
*.log
```

3. 若走**网页上传**路径（第 3 节方案 A），务必只拖入 HTML 文件与 `.gitignore`，**不要整个文件夹拖拽**（否则 `.workbuddy/` 会被一并上传）。

---

## 3. 步骤二：上传到 GitHub

### 方案 A：网页版上传（推荐，无需命令行）

1. 登录 GitHub → 右上角 `+` → **New repository**。
2. 填写：
   - **Repository name**：建议 `ybt2east-lineage`（纯英文、无空格）
   - **Description**：`YBT → EAST 数据血缘地图（静态站点）`
   - **Visibility**：按第 0 节安全提示选择 **Private** 或 Public
   - 勾选 **Add a README file**（可留空，便于生成初始分支）
3. 点击 **Create repository**。
4. 进入仓库 → **Add file** → **Upload files**。
5. 从 `D:\FTP_Share\ybt2east` 中**逐个选中**下列 15 个文件拖入（含 `.gitignore`）：
   `index.html`、`00-Overview.html` … `12-Other.html`、`YBT2EAST-FullLineage.html`、`.gitignore`、`DEPLOY.md`（可选）
6. 下方 **Commit changes** 填写说明（如 `首次发布：血缘站点静态页面`），点击提交。
7. 上传完成后确认：仓库根目录应能看到 `index.html` 与 `.gitignore`，且**不存在** `.workbuddy` 目录。

| 网页上传限制 | 数值 | 本项目 |
| --- | --- | --- |
| 单次上传文件数 | 最多 100 个 | 15 个，满足 |
| 单文件大小 | 建议 < 25 MB（>50 MB 会告警） | 最大 1.78 MB，满足 |

> 注意：GitHub 网页不支持上传空文件夹；`.gitignore` 是隐藏文件，在 Windows 资源管理器中需勾选「查看 → 隐藏的项目」才能看到。

### 方案 B：Git 命令行（本机已具备 Git）

在 Git Bash / PowerShell 中执行（把 `<用户名>`、`<仓库名>` 换成实际值）：

```bash
cd /d/FTP_Share/ybt2east

git init
git add .gitignore *.html
git commit -m "首次发布：YBT→EAST 血缘站点静态页面"
git branch -M main
git remote add origin https://github.com/<用户名>/<仓库名>.git
git push -u origin main
```

提交前建议先核对将纳入版本控制的文件清单，确认**不含** `.workbuddy`：

```bash
git status --short
```

预期输出仅为 `.gitignore`、`DEPLOY.md` 与 15 个 `.html` 文件。

#### 常见报错与处理

| 现象 | 原因 | 处理 |
| --- | --- | --- |
| `Authentication failed` / 提示输入密码后失败 | GitHub 已停用账号密码认证 | 使用 Personal Access Token 作为密码：GitHub → Settings → Developer settings → Personal access tokens → 生成（勾选 `repo` 权限），推送时用户名填 GitHub 账号、密码粘贴 Token |
| `Failed to connect to github.com` / 连接被重置 | 网络限制 | 若本机已有代理（如 7890 端口）：`git config --global http.proxy http://127.0.0.1:7890`；如需取消：`git config --global --unset http.proxy` |
| `remote origin already exists` | 重复添加远程 | `git remote set-url origin https://github.com/<用户名>/<仓库名>.git` |
| `src refspec main does not match any` | 尚未提交 | 先执行 `git commit`，再推送 |
| 提示文件过大 | 误把 `.workbuddy` 加入 | `git rm -r --cached .workbuddy` 后重新提交 |

---

## 4. 步骤三：在 Zeabur 部署

1. 打开 <https://zeabur.com> → **Sign in with GitHub** → 授权（首次需允许 Zeabur 读取仓库）。
2. 控制台点击 **Create Project**（新建项目），若界面提示选择区域/服务器，**建议选香港（HK）或新加坡**，国内访问延迟较低。
3. 进入项目后点击 **Create Service** → 选择 **Git**（从 Git 仓库部署）。
4. 在仓库列表中选中刚上传的仓库（私有仓库首次需点击授权/安装 GitHub App）。
5. Zeabur 自动识别：仓库不含 Node.js / Python / Go 等语言特征 → **按静态模式部署**，入口为根目录 `index.html`，**无需填写构建命令或输出目录**。
6. 等待构建日志出现成功提示（首次约 1–3 分钟）。服务状态变为运行中即部署完成。

### 静态托管机制说明

| 机制 | 说明 |
| --- | --- |
| 首页 | 仓库根目录的 `index.html` 自动作为站点首页 |
| 多页支持 | 支持 MPA/SPA；找不到对应路径时回退返回 `index.html` |
| 自定义 404 | 在根目录放置 `404.html` 即自动生效（可选） |
| 重定向 / 响应头 | 根目录放置 `_redirects`、`_headers` 文件即可配置（可选，语法同 Netlify） |
| 屏蔽路径 | `.git`、`node_modules`、`.venv`、`vendor` 返回 404 |
| 触发重新部署 | 推送新提交自动部署；也可在控制台点击 **Redeploy** 手动触发 |

> **务必确认 `index.html` 位于仓库根目录**。若被放进子文件夹（如 `/site/index.html`），访问根路径将无法展示首页，需要在服务配置中指定根目录。

---

## 5. 步骤四：绑定访问域名

1. 进入该服务的 **Domains** 页签。
2. **方式一（免费、最简）**：点击 **Generate Domain** → 生成 `https://<名称>.zeabur.app`，自动签发 HTTPS 证书，可直接对外分享。
3. **方式二（自有域名）**：点击 **Custom Domain** → 输入域名 → 按界面给出的 DNS 记录在域名服务商处添加 CNAME（如 Cloudflare 的 DNS → Records）。
   - Cloudflare 用户：**SSL/TLS 模式切勿选 Full (Strict)**，会因自签证书导致连接失败；建议橙云开启走 CDN 加速，或关闭橙云直连。
   - 根域名（`example.com`）：Cloudflare 自带 CNAME 展平，可直接使用；其他 DNS 服务商需把 Zeabur 给的 CNAME 解析为 A 记录（用 Dig 工具查询目标主机名得到 IP）。
   - Cloudflare 不支持为三级以上泛域名签发证书（`*.sub.example.com` 不可用）。
4. DNS 生效通常需几分钟，生效后即可用自有域名访问。

---

## 6. 步骤五：上线验收清单

| 序号 | 检查项 | 预期结果 |
| --- | --- | --- |
| 1 | 访问站点根路径 | 展示「主题索引」页（含 12 张主题卡片与列表） |
| 2 | 点击左侧列表任一主题 | 正确跳转对应主题页（如「公共信息」→ `01-PublicInfo.html`） |
| 3 | 顶部导航切换主题 | 13 个导航项均可跳转，当前项高亮 |
| 4 | 打开总览页 | 节点图渲染正常、可缩放拖拽 |
| 5 | 打开单页完整版 | 全量表与节点渲染正常 |
| 6 | 侧栏搜索框 | 输入「贷」等关键字可筛出报表/EAST报表/字段 |
| 7 | 点击节点 | 右侧面板滑出，字段映射明细、来源行号正常显示 |
| 8 | 三类规则颜色 | 直接映射（绿）、码值转换（黄）、加工映射（蓝）与图例一致 |
| 9 | 空主题页 | 「理财」页显示占位说明与待补充清单 |
| 10 | 手机浏览器访问 | 布局可读、无横向溢出 |

> **首次访问慢属正常**：Zeabur 免费套餐在服务空闲后会自动休眠，下一次访问需数秒冷启动。若用于正式演示或汇报，**建议提前 1–2 分钟访问一次唤醒**，或升级 Dev 套餐（US$5/月）避免休眠。

---

## 7. 日常更新流程

`D:\FTP_Share\ybt2east` 中的页面更新后，按以下任一方式发布新版本：

**网页方式**：GitHub 仓库 → 选择同名文件 → `Add file` → `Upload files` 覆盖上传 → `Commit changes`。

**命令行方式**：

```bash
cd /d/FTP_Share/ybt2east
git add *.html
git commit -m "更新：<本次修改说明>"
git push
```

推送完成后 Zeabur 会**自动重新部署**（默认监听分支的每次推送），1–3 分钟后站点即为最新内容。若需强制重发，可在服务页面点击 **Redeploy**。

---

## 8. 常见问题排查

| 现象 | 可能原因 | 处理方式 |
| --- | --- | --- |
| 站点 404 / 空白 | `index.html` 未在仓库根目录；或文件名大小写不一致 | 确认根目录存在 `index.html`；核对链接大小写（本次已校验无误） |
| 页面中文乱码 | 文件被非 UTF-8 工具重存 | 用 UTF-8 重新保存后推送 |
| 页面能开但图表空白、点击无反应 | 内联 JS 报错（多见于手工编辑后引号/括号不闭合） | 浏览器 F12 → Console 查看报错；本地已具备语法校验脚本可复核 |
| 点击主题链接跳转 404 | 文件漏传 | 补传缺失的 `.html` 文件后重新推送 |
| 打开很慢（首屏数秒） | 免费套餐冷启动 / 单页体积较大（1.7 MB） | 演示前先唤醒；如需优化可考虑拆页或压缩 |
| `.workbuddy` 出现在站点上 | 误上传（网页拖拽整目录） | 在 GitHub 删除该目录，并把仓库改为 Private |
| 构建失败、日志提示无法识别项目 | 仓库内混入其他语言的配置文件 | 确认仓库仅含 HTML 与文档文件 |
| 自定义域名无法访问 | DNS 未生效 / Cloudflare SSL 模式为 Full (Strict) | 等待 DNS 传播；把 SSL 模式改为 Flexible 或 Full |

---

## 9. 附录

### 附录 A：GitHub 新建仓库字段速查

| 字段 | 建议值 |
| --- | --- |
| Repository name | `ybt2east-lineage` |
| Description | `YBT → EAST 数据血缘地图（静态站点）` |
| Visibility | Private（含内部数据）/ Public（可公开） |
| Initialize with README | 勾选，便于生成默认分支 |

### 附录 B：Zeabur 关键路径速查

| 事项 | 路径 |
| --- | --- |
| 控制台 | <https://zeabur.com> → Dashboard |
| 新建项目 | Dashboard → **Create Project** |
| 新建服务 | 项目内 → **Create Service** → **Git** |
| 域名绑定 | 服务页 → **Domains** → **Generate Domain / Custom Domain** |
| 环境变量（本项目无需） | 服务页 → **Configuration** → Environment Variables |
| 重新部署 | 服务页 → **Redeploy** |
| 静态站点文档 | <https://zeabur.com/docs/en-US/guides/static> |

### 附录 C：套餐与费用提示

| 套餐 | 价格 | 与本站点相关的要点 |
| --- | --- | --- |
| Free | US$0/月 | 可完成发布；服务空闲自动休眠，下次访问需数秒唤醒；无 SLA |
| Dev | US$5/月（14 天试用） | 常驻不休眠，适合正式演示/长期对外 |
| Pro | US$19/月（14 天试用） | 面向生产负载 |

> 套餐权益以官网 <https://zeabur.com/pricing> 实时信息为准。静态站点本身资源开销极小，免费套餐通常已足够。

### 附录 D：可选增强（按需启用）

| 增强项 | 做法 |
| --- | --- |
| 自定义 404 页 | 在根目录新增 `404.html`（可复制 `index.html` 改文案） |
| 访问统计 | 在页面 `</head>` 前注入第三方统计脚本（如 Umami、GA） |
| 免冷启动 | 升级 Dev 套餐，或配置外部探活定时访问 |
| 内网/受限访问 | 在 Zeabur 服务配置中设置访问密码或改用受控域名 |
