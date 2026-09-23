# 象棋研习 · 中国象棋在线对弈与教学

一个零依赖的静态站点：可在浏览器中直接对弈的中国象棋程序，外加十二篇系统教学文章。

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
├── articles/               十二篇教学文章 + 文章索引
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
├── _check_domain.py        域名可用性探测（DNS，指示性）
└── _verify_deploy.sh       线上内容与本地文件比对
```

## 开发

### 为什么文章页是「生成」的

17 个内容页（12 篇文章 + 文章索引 + 关于/联系/隐私 + 404）由 `site-build/build.js` 统一渲染，而不是手写。
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

#### 第一步：注册域名

**国际注册商**（Porkbun / Namecheap）—— 适合有国际信用卡或 PayPal 的情况：

1. 打开 porkbun.com 或 namecheap.com
2. 搜索想要的域名，加入购物车
3. 注册账号（只需邮箱）并付款
4. 付款后即可在后台管理 DNS

`.xyz` 首年促销价通常在 $1–3，**续费约 $10–15/年**，注意别只看首年价。

**国内注册商**（阿里云 / 腾讯云）—— 适合只有支付宝/微信的情况：

1. 搜索「域名注册」，查询并购买
2. **必须完成实名认证**（上传身份证），这是国内注册商的强制要求
3. 用支付宝或微信付款
4. 实名认证通常几小时内通过

> **不需要 ICP 备案。** 备案只针对「在中国大陆境内托管」的网站。
> 本站托管在 GitHub Pages（境外），因此无需备案。

#### 免费路线：`.pp.ua`（注册与续费永久免费）

nic.ua 的 `.pp.ua` 是少数真正**注册与续费都免费**的域名（价格显示 0.00）。
据官方页面，注册**只需一个可用的手机号**用于激活，不需要信用卡。

1. 打开 https://nic.ua/en/domains/.pp.ua
2. 在搜索框确认域名可用，下单（0.00）
3. 注册 nic.ua 账号（邮箱即可）
4. **激活**（必需，用于防机器人抢注）：
   - 若收到含激活码的短信 → 到 https://pp.ua 输入
   - **若收不到短信**（非乌克兰号码常见）→ 用 Telegram 机器人 [@ppuabot](https://t.me/ppuabot) 激活
5. `.pp.ua` 只做 NS 委派、不托管 DNS，因此还需一个 DNS 服务商
   （Cloudflare 免费版即可）来放 A / CNAME 记录

限制与代价：

- 域名长度 3–63 字符，可用拉丁字母、西里尔字母，甚至 emoji
- 每 30 天最多注册 3 个
- 委派期 1 年，需在到期前 60 天内续期（免费，但别忘了）
- **WHOIS 联系方式始终公开且无法隐藏**（该域名区的强制规定）——
  你的姓名与手机号会对外可见
- 到期后有 28 天优先续期期，之后需付费恢复（996 UAH）且不保证成功

> ⚠️ 另需注意：`.pp.ua` 属免费二级域名，
> **Google AdSense 是否接受此类域名尚未证实**。
> 若以通过 AdSense 审核为首要目标，`.xyz` 等正规 TLD 更稳妥。

#### 第二步：配置 DNS

在注册商的 DNS 管理页面添加以下记录：

| 类型 | 主机 | 值 |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `pdaepdp.github.io` |

#### 第三步：一键切换

```bash
bash xiangqi-tests/_setup_domain.sh yourdomain.com
```

脚本会写入 `CNAME`、把 `site-build/build.js` 里的 `SITE` 改为新域名、
重新生成全部页面（canonical / og:url / sitemap / 绝对路径前缀随之更新）、
跑一次链接校验（失败即中止，不会推送）、推送、设置 Pages cname，
最后轮询 DNS 与 HTTPS 并确认返回的页面标题确为本站。

> 站点路径前缀由 `build.js` 中的 `BASE` 常量（从 `SITE` 推导）统一管理。
> 换域名后只需改 `SITE` 一行，全站绝对路径会一起更新 ——
> 不要在 HTML 里硬编码路径。

#### 附：域名可用性探测

```bash
python xiangqi-tests/_check_domain.py name1.xyz name2.com
```

基于 DNS 查询给出「已注册 / 可能可用 / 无法判断」。
**注意这只是指示性的**：已注册但未做 NS 委派的域名会被误判为可用，
最终以注册商页面为准。

## 待办

- **AdSense 申请**：内容与隐私政策已就绪，**唯一剩余门槛是自有根域名**。
  `pdaepdp.github.io` 属共享域下的免费子域，AdSense 不接受；
  需绑定自有域名后才能提交审核。
- `ads.txt` 目前只有注释，通过审核后需填入 Google 提供的
  `google.com, pub-XXXXXXXX, DIRECT, f08c47fec0942fa0`。
