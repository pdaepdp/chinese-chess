# 象棋研习 · 中国象棋在线对弈与教学

一个零依赖的静态站点：可在浏览器中直接对弈的中国象棋程序，外加六篇系统教学文章。

**线上地址**：https://pdaepdp.github.io/chinese-chess/

---

## 特点

- **完整规则引擎**：将帅照面、蹩马腿、塞象眼、炮打隔子、困毙判负，
  以及三次重复局面与 60 回合无吃子的判和规则。
- **人机对弈**：Alpha-Beta 剪枝 + 静态搜索，三档难度（2/3/4 层）。
- **可选执子**：执红先行或执黑后行；执黑时棋盘自动翻转，由电脑先行。
- **中文记谱**：自动生成标准记谱（含「前/后」缀），可在对局中对照学习。
- **无障碍**：支持纯键盘操作与屏幕阅读器播报，并尊重系统的「减少动态效果」。
- **零依赖**：不使用任何框架、CDN 或后端服务，全部为原生 HTML/CSS/JS。

## 目录结构

```
xiangqi/                    ← 站点根目录（= 部署内容，GitHub Pages 的 main 分支）
├── index.html              对弈主页面（单文件，内联样式与脚本）
├── articles/               六篇教学文章 + 文章索引
├── about.html              关于本站
├── contact.html            联系我们
├── privacy.html            隐私政策（AdSense 要求）
├── 404.html                自定义 404
├── favicon.svg             站点图标
├── assets/
│   ├── site.css            内容页共用样式
│   └── og.png              社交分享预览图（1200×630）
├── robots.txt / sitemap.xml / ads.txt

site-build/                 ← 构建工具（不部署）
├── build.js                内容页生成器：持有全部文章内容 + HTML 模板
└── og-card.html            og.png 的源文件（用无头 Chrome 截图生成）

xiangqi-tests/              ← 测试与运维脚本（不部署）
├── _test_engine.js         规则引擎断言（34 项）
├── _check_links.js         站内链接与页面结构校验
├── _check_a11y.js          可访问性静态检查
├── _selfplay.js            自我对弈稳定性
├── _bench_ai.js            各难度耗时
├── _depthstudy.js          按深度统计收局率
├── _setup_domain.sh        一条命令绑定自有域名
└── _verify_deploy.sh       线上内容与本地文件比对
```

## 开发

### 为什么文章页是「生成」的

11 个内容页由 `site-build/build.js` 统一渲染，而不是手写。
好处是导航、页脚、meta 标签不会在页面之间漂移，新增一篇文章只需在
`ARTICLES` 数组里加一个对象。**改文章内容后必须重新生成**，否则改动不会生效。

```bash
node site-build/build.js      # 重新生成全部内容页
```

### 测试

```bash
node xiangqi-tests/_test_engine.js    # 规则正确性（应输出 ALL PASS, 34 passed）
node xiangqi-tests/_check_links.js    # 链接与结构（应输出 全部链接有效）
node xiangqi-tests/_check_a11y.js     # 可访问性（应输出 未发现结构性问题）
node xiangqi-tests/_selfplay.js       # 自我对弈（应输出 6/6 局正常结束）
```

`_test_engine.js` 会从 `index.html` 中**抽取引擎代码段**在 Node 中运行，
因此修改引擎后无需额外步骤即可回归。

规则正确性以 **perft** 对照公开参考值校验：初局合法着法数在深度 1/2/3 上
应为 `44 / 1920 / 79666`，与参考值完全一致。

## 部署

站点托管在 GitHub Pages，发布分支为 `main`，根目录即站点内容。

```bash
cd xiangqi
git add -A && git commit -m "..." && git push
```

推送后 GitHub Pages 通常 1 分钟内完成构建（构建期间返回 404 属正常）。

### 绑定自有域名

```bash
bash xiangqi-tests/_setup_domain.sh yourdomain.com
```

脚本会写入 `CNAME`、把 `site-build/build.js` 里的 `SITE` 改为新域名、
重新生成全部页面（canonical / og:url / sitemap / 绝对路径前缀随之更新）、
跑一次链接校验（失败即中止，不会推送）、推送、设置 Pages cname，
最后轮询 DNS 与 HTTPS 并确认返回的页面标题确为本站。

DNS 需要提前配置：

| 类型 | 主机 | 值 |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `pdaepdp.github.io` |

> 站点路径前缀由 `build.js` 中的 `BASE` 常量（从 `SITE` 推导）统一管理。
> 换域名后只需改 `SITE` 一行，全站绝对路径会一起更新 ——
> 不要在 HTML 里硬编码路径。

## 待办

- **AdSense 申请**：内容与隐私政策已就绪，**唯一剩余门槛是自有根域名**。
  `pdaepdp.github.io` 属共享域下的免费子域，AdSense 不接受；
  需绑定自有域名后才能提交审核。
- `ads.txt` 目前只有注释，通过审核后需填入 Google 提供的
  `google.com, pub-XXXXXXXX, DIRECT, f08c47fec0942fa0`。
