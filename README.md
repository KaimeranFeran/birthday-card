# 🎂 生日贺卡 · Birthday Card

一张会「吹蜡烛」的生日贺卡网页。纯静态单文件，零依赖、零构建，直接托管在 GitHub Pages 上。

## 在线访问

https://kaimeranferan.github.io/birthday-card/

## 功能

- **星空 + 信封封面**：点击打开贺卡
- **纯 CSS 手绘蛋糕**：三层奶油、樱桃、摇曳的烛火与光晕
- **吹蜡烛**：用麦克风检测吹气（`getUserMedia` + `AnalyserNode` 音量分析），吹灭后冒烟、撒彩带、响生日铃
- **兜底方案**：麦克风被拒/不存在时，点击蜡烛、长按蛋糕、按空格键都能吹灭
- **定制收件人**：URL 参数定制名字与祝福语
- **分享按钮**：一键复制当前链接
- **响应式 + 无障碍**：手机竖屏优先，支持 `prefers-reduced-motion`、键盘操作、聚焦样式

## 定制成给特定人

在链接后加参数即可，一个页面能发给不同的人：

```
https://kaimeranferan.github.io/birthday-card/?to=小美&from=阿明&msg=生日快乐，永远十八岁
```

| 参数 | 说明 | 长度上限 |
|---|---|---|
| `to` | 收件人名字，会出现在标题和祝福语里 | 24 字 |
| `from` | 署名，显示在祝福语右下角 | 24 字 |
| `msg` | 自定义祝福语，替换默认文案 | 120 字 |

分享给朋友时，**中文参数请先做 URL 编码**，例如：

```js
const url = 'https://kaimeranferan.github.io/birthday-card/?to=' + encodeURIComponent('小美')
          + '&from=' + encodeURIComponent('阿明');
```

不带参数时使用通用文案，可以直接分享。

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
