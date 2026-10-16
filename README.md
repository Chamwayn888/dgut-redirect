# 东莞理工学院 · 炫酷跳转页

打开域名 → 播放一段徽章动画（绿身形象 + 肚子上印校区校徽 + 旋转文字环「东莞理工学院」+ 环形进度条）→ 约 3 秒后自动跳转到
<https://www.dgut.edu.cn/>。点一下屏幕或按任意键可以立刻跳转。

效果复刻自 `宇宙奶龙大学.com` 的同款跳转页。

---

## 文件清单

| 文件 | 作用 | 上线后还要改吗 |
| --- | --- | --- |
| `index.html` | 主页面，动画、倒计时、跳转逻辑全在这里 | 要改 2 处，见下 |
| `ring.svg` | 环绕校名的文字环（已生成「东莞理工学院」） | 不用 |
| `core.webp` | 徽章中心图案——绿身（莞工校色）+ 肚子上印校区校徽（现代浏览器优先用这张，24 KB） | 不用 |
| `core.png` | 中心图案的兜底版本（老浏览器用这张，157 KB） | 不用 |
| `core-mascot.svg` | 备选中心图案：原创手绘吉祥物＋学士帽 | 不用（默认没启用） |
| `favicon.svg` | 浏览器标签页小图标 | 可选 |
| `og.png` | 微信/QQ 分享卡片图（1200×630） | 可选 |
| `CNAME` | GitHub Pages 绑定自定义域名用 | **必须改** |
| `tools/make_ring.py` | 重新生成 `ring.svg` 的脚本（改文案用） | 不用 |
| `tools/make_og.py` | 重新生成 `og.png` 的脚本 | 不用 |
| `tools/make_core_green.py` | 重新生成中心图案：黄底原图 → 莞工绿 + 肚子印校徽 | 不用 |
| `tools/verify_core.py` | 校验中心图案的配色与文件完整性 | 不用 |
| `tools/preview.py` | 本地预览脚本 | 不用 |

> `core.webp` 和 `core.png` 是同一张图的两份，用 `<picture>` 做格式回退：支持 WebP 的浏览器只下载 24 KB 那份，不支持的自动退回 PNG（157 KB）。两张要同时保留，**不要只删其中一张**。

## 域名配置（已填好）

域名是 **`广东奶龙大学.vip`**，中文域名必须转成 Punycode 才能配解析和 GitHub Pages：

| 写法 | 值 |
| --- | --- |
| 中文（给人看的） | `广东奶龙大学.vip` |
| Punycode（给机器用的） | `xn--xhq02y0gz4eg2fdt3k.vip` |
| 带 www | `www.xn--xhq02y0gz4eg2fdt3k.vip` |

已经改好的两处：

1. **`CNAME` 文件** = `xn--xhq02y0gz4eg2fdt3k.vip`（GitHub Pages 必须填 Punycode 版本）
2. **`index.html` 里的 `og:image` / `og:url`** = `https://xn--xhq02y0gz4eg2fdt3k.vip/...`
   分享卡片图必须是绝对地址，写相对路径微信抓不到。

> 换域名时记得上面两处都要跟着换，`CNAME` 里**不带** `www.`。

## 中文域名的三个限制（重要）

1. **不能绑阿里云 OSS / CDN。** 阿里云官方明确写着 OSS「不支持绑定中文域名」，CDN 也不支持。所以后续想换国内加速方案时，中文域名这条路是堵死的，只能走 GitHub Pages 或自己的服务器。
2. **微信/QQ 里分享可能抓不到缩略图。** 中文域名的 IDN 解析对爬虫不友好，所以 `og:image` 我全部写成了 Punycode 绝对地址。
3. **输入习惯。** Chrome / Edge / 新版 Safari 直接输中文能通，老浏览器和部分 App 内置浏览器需要输 Punycode。

## 常见改动

```js
// index.html 底部 <script> 里
const TARGET = 'https://www.dgut.edu.cn/';   // 跳转目标
const NAME   = '东莞理工学院官网';            // 文案里显示的目标名
const EASTER = '宇宙莞工大学';                // 键盘依次按 d、g 触发的彩蛋
const DELAY  = reduce ? 1200 : 2600;         // 停留时长，越大跳得越晚
```

## 本地预览

**别直接双击 `index.html`**，一定要起个本地服务，否则跳转会把你带走到官网、什么都看不清：

```bash
# 用项目自带的预览脚本（会以 ?stay 参数打开，循环播放动画、不跳转）
python tools/preview.py

# 或者手动起服务，然后访问 http://localhost:8000/?stay
python -m http.server 8000
```

加 `?stay` 参数 = 演示模式：动画循环播放，永远不跳转。去掉就会真的跳走。

## 换中心图案

### 重新生成绿色版（当前用的就是这一版）

当前 `core.png` / `core.webp` 由 `tools/make_core_green.py` 生成：把黄底原图换成莞工绿，
并把官方校徽「印」在肚子上。脚本的输入固定取 **`promo/brand/core_original_yellow.png`（黄底原图备份）**，
所以可以重复运行、结果幂等，不会二次叠加。

```bash
python tools/make_core_green.py     # 重新生成 core.png / core.webp
python tools/verify_core.py         # 校验配色与文件完整性
```

想调效果就改脚本顶部的参数区（全部带注释）：

| 参数 | 作用 |
| --- | --- |
| `BODY_VAL_GAIN` | 身体明度增益，越小越接近校徽绿的深色（当前 0.80） |
| `BELLY_RX` / `BELLY_RY` / `BELLY_CY` | 肚子椭圆的大小与位置（上边缘不能越过交叉手臂，约 y=400） |
| `PRINT_SCALE` | 校徽大小 = 肚子内接圆直径 × 该系数（当前 0.90 → 147 px） |
| `PRINT_DY` | 校徽上下偏移 |
| `SHADE_LO` / `SHADE_HI` | 曲面明暗调制范围，收窄可让校徽上下更均匀 |

换色只作用于色相 15–140° 的像素：实测原图身体落在 28–53°、虹膜绿落在 70–131°（后者 100% 在双眼内），
所以这条带子能覆盖身体又完整保护眼睛。肚子靠椭圆几何遮罩单独处理，压到低饱和成为浅薄荷，
这样校徽印上去才有对比度。

### 换成你自己的图片

1. 把图片裁成正方形（建议 720×720 或更大，**要带透明通道**），命名 `core.png` 放进本目录，覆盖原文件；
2. 有 WebP 的话同时导出一份 `core.webp` 覆盖，没有就照常跑（浏览器会退回 PNG）；
3. 只替换文件即可，`index.html` 不用动。

> 图案要留白边：徽章是圆形，四角会被裁掉，人物别顶到边缘。
>
> 黄底原图备份在 `promo/brand/core_original_yellow.png`（和 `.webp`）。想回到黄底版，
> 把它们复制回本目录覆盖 `core.png` / `core.webp` 就行。

### 换回原创吉祥物

把 `index.html` 里这段：

```html
<picture>
  <source srcset="core.webp" type="image/webp">
  <img class="core" src="core.png" alt="" width="720" height="720" fetchpriority="high">
</picture>
```

替换成：

```html
<img class="core" src="core-mascot.svg" alt="" width="720" height="720" fetchpriority="high">
```

## 改校名文案

改 `tools/make_ring.py` 顶部的 `TEXT`，然后重新生成：

```bash
python tools/make_ring.py
```

---

## 合规提醒（重要）

- 本页与东莞理工学院**无任何官方关联**。这条声明写在页面底部（`<footer class="foot">` 里的 `.disclaimer` 那行小字），**请务必保留**——它是这个页面和「冒充官网」之间最重要的界线。
- **中心形象用的是有版权的商业 IP**（权利人：第七印象文化传媒（深圳）有限公司），当前版本在它基础上做了换色并叠加了校徽。这里属于非商业性的粉丝趣味使用，请不要拿这个页面做任何商业用途、引流或接广告，也别用于可能损害该形象声誉的场景。要正式公开发布，更稳妥的做法是换成你自己的图或 `core-mascot.svg`。
- 校名、校徽等标识的权利归东莞理工学院所有。**不要**把页面做得像学校官网，不要放「官方网站」「官方入口」这类字样，也不要放任何会收集访客信息的表单。
- 如果域名做了 ICP 备案，必须去掉 `index.html` 里备案号那一行的注释并填上真实备案号——法规要求展示在网站底部。

## 目录结构

```
dgut-redirect/
├── index.html          主页面
├── ring.svg            文字环
├── core.webp           中心图案（绿身 + 肚子印校徽，WebP）
├── core.png            中心图案（同上，PNG 兜底）
├── core-mascot.svg     备选中心图案（原创吉祥物）
├── favicon.svg         站点图标
├── og.png              分享卡片图
├── CNAME               GitHub Pages 域名绑定文件
├── README.md           本文件
└── tools/
    ├── make_ring.py    生成 ring.svg
    ├── make_og.py      生成 og.png
    ├── make_core_green.py  生成中心图案（换绿 + 印校徽）
    ├── verify_core.py  校验中心图案
    └── preview.py      本地预览
```
