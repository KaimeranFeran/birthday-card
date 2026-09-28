# 🎂 生日贺卡 · 致徐赫

一张会「吹蜡烛」的生日贺卡网页。纯静态单文件，零依赖、零构建，直接托管在 GitHub Pages 上。

**祝贺人：徐赫** ｜ **日期：2026 年 9 月 28 日**

## 在线访问

https://kaimeranferan.github.io/birthday-card/

## 设计风格：Claude (Anthropic) — Warm Editorial

参考 Anthropic / Claude 官方品牌设计语言，暖色编辑风。

### 配色 token

| Token | Hex | 用途 |
|---|---|---|
| `--bg-primary` | `#f4f3ee` | 奶油纸底（页面背景） |
| `--bg-secondary` | `#eeede6` | 浅表面提升（卡片） |
| `--bg-inverse` | `#191817` | 近黑反色（toast） |
| `--text-primary` | `#191817` | 墨黑正文 |
| `--text-secondary` | `#5a554e` | 暖灰次要文字 |
| `--text-muted` | `#8a847a` | 弱化标签 |
| `--accent` | `#c96442` | **赤陶色**，全局唯一强调色 |
| `--accent-hover` | `#b55738` | 赤陶悬停 |
| `--accent-soft` | `#e89268` | 柔赤陶 |
| `--border` | `#d8d3c8` | 1px 描边 |
| `--warning` | `#c98a42` | 烛火暖橙 |
| `--success` | `#6b7a3d` | 樱桃梗绿 |

### 遵循的设计规则

- **单一强调色**：赤陶色每屏只出现一处主色块（火漆印 / 火漆心 / 主按钮 / 樱桃），不与自身竞争。
- **平铺无阴影**：深度来自表面色差 + 1px 描边 + 字体粗细对比，不用 drop-shadow、不用 glassmorphism。
- **衬线标题 + 人文无衬线正文**：标题 `Tiempos Headline / Iowan Old Style / 宋体`，正文 `Styrene A / Inter / 苹方`，全页不超两种字族。
- **无紫粉渐变**：原版的深紫星空+金粉渐变全部移除，改为奶油纸底 + 极淡纸张纤维纹理。
- **hover 不用位移/缩放**：按钮只变背景色。
- 等宽字体（mono）用于 eyebrow 标签与日期，大写+宽字距。

> 配色来源：Claude 官方设计 token（`#cc785c` 主色族）与社区整理的 Anthropic Warm Editorial DESIGN.md（`#c96442` 赤陶 / `#f4f3ee` 奶油 / `#191817` 墨黑）。

## 功能

- **奶油信笺封面**：日期眉标 + 火漆信封 + 衬线大标题，点击打开贺卡
- **纯 CSS 手绘蛋糕**：三层奶油、赤陶樱桃、摇曳烛火与暖色光晕
- **吹蜡烛**：用麦克风检测吹气（`getUserMedia` + `AnalyserNode` 音量分析），吹灭后冒烟、撒彩带、响生日铃
- **兜底方案**：麦克风被拒/不存在时，点击蜡烛、长按蛋糕、按空格键都能吹灭
- **定制收件人**：URL 参数定制名字与祝福语
- **分享按钮**：一键复制当前链接
- **响应式 + 无障碍**：手机竖屏优先，支持 `prefers-reduced-motion`、键盘操作、聚焦样式

## 定制成给特定人

默认祝贺人已经是**徐赫**。也可以在链接后加参数换成别人：

```
https://kaimeranferan.github.io/birthday-card/?to=小美&from=阿明&msg=生日快乐，永远十八岁
```

| 参数 | 说明 | 默认值 | 长度上限 |
|---|---|---|---|
| `to` | 收件人名字，会出现在标题和祝福语里 | `徐赫` | 24 字 |
| `from` | 署名，显示在祝福语右下角 | 空（不显示） | 24 字 |
| `msg` | 自定义祝福语，替换默认文案 | 通用文案 | 120 字 |

分享给朋友时，**中文参数请先做 URL 编码**，例如：

```js
const url = 'https://kaimeranferan.github.io/birthday-card/?to=' + encodeURIComponent('小美')
          + '&from=' + encodeURIComponent('阿明');
```

不带参数时，默认就是给徐赫的版本，可以直接分享。

## 修改日期

日期在 `index.html` 的 JS 开头（搜索 `var NOW`）：

```js
var NOW = {
  y: 2026, m: 9, d: 28,
  weekday: 'Monday'
};
```

改为其他生日只需改这几行，封面眉标与贺卡日期行会同步更新。

## 本地预览

直接双击 `index.html` 即可。

> 麦克风吹气功能需要 HTTPS 或 `localhost` 环境（浏览器安全策略），`file://` 下会自动降级为点击吹灭。

起个本地服务器测试完整效果：

```bash
python -m http.server 8000
# 然后访问 http://localhost:8000
```

## 修改内容

纯静态文件，改完直接推送即可，无需构建：

```bash
git add -A
git commit -m "update wording"
git push
```

推送后最长约 10 分钟生效。

## 部署说明（GitHub Pages）

- 站型：**项目站**，仓库名 `birthday-card`
- 发布源：`main` 分支，`/`（仓库根目录）
- `.nojekyll` 用于禁用 Jekyll 处理，避免以下划线开头的资源被忽略
- 因为项目站地址含子路径 `/birthday-card/`，**所有资源引用必须用相对路径**。本项目 CSS/JS 全部内联，从根上避免 404

完整方案与调查记录见 [PLAN.md](./PLAN.md)。

## 文件结构

```
.
├─ index.html    # 贺卡全部内容（HTML + 内联 CSS + 内联 JS）
├─ 404.html      # 404 回退页
├─ .nojekyll     # 禁用 Jekyll
├─ README.md     # 本文件
└─ PLAN.md       # 部署方案文档
```

## 许可

个人使用随意。祝生日快乐 🎉
