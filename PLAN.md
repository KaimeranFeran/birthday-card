# 生日贺卡网页 — GitHub Pages 部署方案

## 一、调查结论：GitHub Pages 怎么用

### 1.1 两种站点类型

| 类型 | 仓库名要求 | 访问地址 | 数量限制 |
|---|---|---|---|
| 用户站 | `<用户名>.github.io` | `https://<用户名>.github.io/` | 每账号仅 1 个 |
| **项目站（本项目采用）** | 任意名，如 `birthday-card` | `https://<用户名>.github.io/birthday-card/` | 不限 |

### 1.2 两种发布源

| 方式 | 适用 | 说明 |
|---|---|---|
| **从分支部署** | 纯静态文件、无构建步骤 | 指定分支 + 文件夹（`/` 或 `/docs`），push 即发布 |
| GitHub Actions | 需构建（Vite/React 等） | 用 `upload-pages-artifact` + `deploy-pages` |

> 本项目是单文件纯 HTML，**用"从分支部署"最简单**：仓库根目录放 `index.html`，无需 Actions 工作流，无构建耗时。

### 1.3 关键注意事项

- 入口文件必须是源文件夹顶层的 `index.html`（或 `index.md` / `README.md`）。
- 从分支发布时，GitHub 默认用 **Jekyll** 构建。文件名以下划线开头（如 `_next/`）会被 Jekyll 忽略 → 需加空的 **`.nojekyll`** 文件禁用 Jekyll。本项目一并加上，防止资源目录被吞。
- 发布有延迟，最长约 10 分钟；一小时后仍不更新才需要排查。
- Pages 站点**在公网公开可访问**，即使仓库设为 Private（付费计划）。所以不要往站点目录放隐私内容。
- 必须使用管理员权限 + 已验证邮箱的人来 push 才能触发构建。

### 1.4 用量限制（够用，但记录一下）

| 项目 | 限制 |
|---|---|
| 源仓库大小 | 建议 ≤ 1 GB |
| 已发布站点大小 | ≤ 1 GB |
| 部署超时 | 10 分钟 |
| 带宽 | 软限制 100 GB/月 |
| 构建频率 | 软限制 10 次/小时（自定义 Actions 不适用） |
| 用途 | 禁止电商/商业 SaaS、密码信用卡等敏感交易 |

### 1.5 URL 与资源路径的坑（项目站必看）

项目站地址是 `https://user.github.io/birthday-card/`，**根路径不是域名根**。因此：
- 所有资源引用必须用**相对路径**：`./style.css` 或 `style.css`，绝不能写 `/style.css`。
- 本项目采用**单文件**设计，CSS/JS 全部内联，从根上规避此问题。
- 附带 `404.html`，链接写错时给一个友好回退页。

---

## 二、实施方案

### 2.1 技术选型

- **单文件 `index.html`**：HTML + 内联 CSS + 内联 JS，零依赖、零构建。
- 无 npm、无打包器、无框架。双击文件即可本地预览。
- 字体用系统字体栈，不引外部 CDN（避免墙内加载慢 / 离线失效）。

### 2.2 功能设计（饼图）

页面流程：**信封/封面 → 点击打开 → 贺卡主体 → 吹蜡烛 → 熄灭庆祝**

1. **封面**：深色夜空 + 星点，中央信封/礼物盒，提示"点击打开"。
2. **贺卡主体**：
   - 蛋糕（纯 CSS 绘制：三层奶油 + 樱桃 + 蜡烛）。
   - 蜡烛带火焰，CSS 动画摇曳（`@keyframes flicker`）。
   - 祝福文案，支持 URL 参数定制（见 2.3）。
3. **吹蜡烛**：
   - 主方案：`getUserMedia` 采集麦克风 → `AnalyserNode` 算音量 → 超阈值判定"吹气" → 火焰熄灭 + 烟雾粒子动画。
   - 兜底：点击 / 长按蜡烛也能吹灭；麦克风被拒、无设备、非 HTTPS 时自动降级并提示。
   - 微妙交互：把手机凑近麦克风吹，或对屏幕吹气；加一点音效（Web Audio 合成，无需 mp3）。
4. **吹灭后**：彩带粒子（canvas 或纯 CSS `div`）、`Happy Birthday` 标题亮起、可选照片/文字区域。
5. **响应式**：手机竖屏优先，`clamp()` 控制字号与蛋糕尺寸。
6. **无障碍**：`prefers-reduced-motion` 下关闭粒子动画；按钮有 `aria-label`；键盘可操作。

### 2.3 定制化（一个页面发给不同人）

通过 URL 查询参数：

```
https://user.github.io/birthday-card/?to=小美&from=阿明&msg=生日快乐
```

- `to`：收件人名字，显示在标题与蛋糕下方。
- `from`：署名。
- `msg`：自定义祝福语。
- 未传参数时用默认文案（通用版），方便直接分享。
- 用 `URLSearchParams` 读取，**用 `textContent` 写入 DOM**，绝不 `innerHTML`，防止 XSS。

### 2.4 目录结构

```
birthday-card/
├─ index.html      # 全部内容（HTML+CSS+JS 内联）
├─ 404.html        # 404 回退页
├─ .nojekyll       # 禁用 Jekyll 处理
├─ README.md       # 仓库说明 + 使用方式 + 定制参数说明
├─ PLAN.md         # 本方案文档
└─ .gitignore
```

### 2.5 部署流程（实际执行步骤）

```bash
# 1. 本地初始化
cd E:/Astra/birthday-card
git init -b main
git add .
git commit -m "feat: birthday card with candle blowing"

# 2. 创建远程仓库并推送（gh CLI，已登录 KaimeranFeran）
gh repo create birthday-card --public --source=. --remote=origin --push

# 3. 开启 Pages（从 main 分支根目录发布）
gh api -X POST repos/KaimeranFeran/birthday-card/pages \
  -f "source[branch]=main" -f "source[path]=/"

# 4. 查询状态
gh api repos/KaimeranFeran/birthday-card/pages --jq '.html_url,.status'
```

- 用 **public 仓库**：GitHub Free 下 Pages 站点在私有仓库需要付费计划；公开仓库 Actions/Pages 全部免费。
- 若仓库非空或已存在，`gh repo create` 会失败 → 改用 `git remote add` + `git push -u origin main`。
- 若 Pages 已启用，POST 会返回 409 → 改用 `gh api -X PUT ...pages` 更新配置。

### 2.6 验收检查清单

- [ ] `https://KaimeranFeran.github.io/birthday-card/` 返回 200，显示封面。
- [ ] 点击封面能进入贺卡，蛋糕与蜡烛正常渲染（手机 + 桌面各试一次）。
- [ ] 麦克风吹气能熄灭蜡烛；拒绝权限后点击也能熄灭（降级生效）。
- [ ] 带 `?to=&from=&msg=` 参数时文案正确，且不乱码（URL 用 `encodeURIComponent`）。
- [ ] 打开 DevTools → Network，确认无 404 资源（相对路径正确）。
- [ ] 手机浏览器实测：布局不溢出、无横向滚动条。
- [ ] `prefers-reduced-motion: reduce` 下动画关闭。

### 2.7 风险与对策

| 风险 | 对策 |
|---|---|
| 麦克风需 HTTPS + 用户授权 | Pages 自带 HTTPS；提供点击兜底，不做强制 |
| 首次部署延迟最长 10 分钟 | 用 `gh api .../pages` 轮询 `status`，不反复重推 |
| 中文 URL 参数乱码 | 分享链接中的中文由生成方 `encodeURIComponent`；读取端 `URLSearchParams` 自动解码 |
| 项目站子路径导致资源 404 | 全内联 + 相对路径 + 单文件，天然免疫 |
| 后续想改文案 | 直接改 `index.html` 后 `git push`，无需构建 |
| 想换自定义域名 | 仓库 Settings → Pages → Custom domain，配 CNAME 记录；Actions 部署时需在设置里配域，`CNAME` 文件会被忽略 |

---

## 三、后续可扩展

- 自定义域名（需自备域名 + DNS CNAME 记录 + 强制 HTTPS）。
- 换成 Vite/React 时应改为 GitHub Actions 发布源（`upload-pages-artifact` + `deploy-pages`）。
- 加照片墙、多页流程、音乐（注意版权，音频文件计入 1 GB 限制）。
