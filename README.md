# Heartthrob · 炫酷表白代码

一个**纯前端、零依赖**的表白特效页面：粉色代码雨开场倒计时 → 粒子汇聚成字 → 流动粒子爱心 + 螺旋星盘 + 照片飞升 + 玫瑰飘落。

双击 `index.html` 即可运行，无需安装任何环境。

## 效果组成

| 模块 | 说明 | 所在位置 |
| --- | --- | --- |
| 开场代码雨 | 粉色字符雨 + 倒计时 3/2/1 | `index.html` 第一个 `<script>` 块 |
| 粒子文字 | 粒子从四周汇聚成字，再散开 | `startParticleText()` / `sampleText()` |
| 流动粒子爱心 | 心形方程驱动的粒子，带心跳脉动 | `=== 流动粒子爱心 ===` 模块 |
| 粒子旋涡 | 爱心下方的漏斗状螺旋盘 | `=== 粒子旋涡 ===` 模块 |
| 照片飞升 | `img/` 下的图片循环上升 | `=== 照片飞升 ===` 模块 |
| 漂浮玫瑰 | 50 朵 🌹 缓慢上升 | `=== 漂浮玫瑰 ===` 模块 |
| 左侧滚动代码 | 装饰用的代码面板（可替换成情书） | `=== 左侧滚动代码 ===` 模块 |
| 背景星光 / 光斑 | 闪烁星点与焦外光斑 | `=== 背景星光 ===` / `=== 漂浮光斑 ===` |

## 快速开始

```bash
# 方式一：直接双击 index.html（最简单）

# 方式二：本地起个服务（推荐，图片加载更稳定）
npx serve .
# 或
python -m http.server 8080
```

然后浏览器打开 `http://localhost:8080`。

## 目录结构

```
heartthrob/
├── index.html      # 全部逻辑都在这一个文件里（HTML + CSS + JS）
├── img/            # 照片素材：1.jpg ~ 7.jpg
└── README.md
```

---

## 一、修改名称 / 文案

### 1. 浏览器标签页标题

`index.html` 第 6 行：

```html
<title>JY Love</title>
```

改成你要的名字即可，例如 `<title>致小美 · 我爱你</title>`。

### 2. 开场文字（重点！）

找到这段代码（约第 324 行）：

```js
// 倒计时 3→2→1,再逐句展示文字
const steps=[["3",900],["2",900],["1",900],["JY",2000],["祝你",2000],["天天开心",2000]];
```

格式是 `[文字, 显示毫秒数]`，数组里每一项就是一屏字。想改就照着加/删/换：

```js
const steps=[
    ["3",900],
    ["2",900],
    ["1",900],
    ["小美",2000],
    ["遇见你",1800],
    ["是我最大的幸运",2200],
    ["做我女朋友好吗",3000]
];
```

> 提示：
> - 中文、英文、emoji 都支持；
> - 单屏字太多会被自动缩小（`sampleText()` 里的自适应逻辑），建议每屏不超过 8 个字；
> - 数字越小切换越快，`1000` = 1 秒。

### 3. 左侧滚动代码（可当情书用）

约第 343 行起是 `const codeLines=[...]`，里面每一行字符串就是面板上的一行"代码"。可以直接替换成你想说的话：

```js
const codeLines=[
"/* 致小美 */",
"function love(you) {",
"    return you.smile ? forever : forever;",
"}",
"",
"while (true) {",
"    missYou();",
"}"
];
```

注意：数组里**至少要保留几行**，否则滚动动画会很短；增删行不影响运行，行号会自动连续。

---

## 二、修改图片

### 1. 直接替换（最简单，推荐）

把你的照片按下面命名放进 `img/` 目录，**覆盖**原文件即可，不用改任何代码：

```
img/1.jpg  img/2.jpg  img/3.jpg  img/4.jpg  img/5.jpg  img/6.jpg  img/7.jpg
```

建议：正方形或接近方形的竖图（代码里是居中等比裁切填充），单张控制在 500KB 以内，页面加载更快。

### 2. 增加 / 减少图片数量

找到照片加载部分（约第 677 行）：

```js
// 加载 img 文件夹下全部图片(15 为 png,其余 jpg)
const images=[];
for(let i=1;i<=7;i++){
    const img=new Image();
    img.src="img/"+i+(i===15?".png":".jpg");
    images.push(img);
}
```

- **想用 10 张**：把 `i<=7` 改成 `i<=10`，并保证 `img/8.jpg`、`img/9.jpg`、`img/10.jpg` 存在；
- **想只用 3 张**：把 `i<=7` 改成 `i<=3`（多余的 `4~7.jpg` 可以删掉）；
- **想用 png**：把 `".jpg"` 改成 `".png"`，文件名对应改成 `1.png` 等；
- **想用自定义文件名**（如 `xiaomei.jpg`）：直接写死列表更直观：

```js
const files=["xiaomei.jpg","travel.jpg","cat.jpg","us.jpg"];
const images=files.map(f=>{
    const img=new Image();
    img.src="img/"+f;
    return img;
});
```

> 加载失败的图片会被自动忽略（`readyImages()` 过滤），不会导致白屏，所以放心改。

### 3. 调整照片的显示参数

同一个模块里（约第 686~705 行）：

```js
const COUNT=14;                                  // 同屏照片数量（车道数）
p.size=Math.min((52+Math.random()*46)*DPR,laneW*0.86); // 缩略图尺寸
p.vy=(0.05+Math.random()*0.06)*H;                // 上升速度
```

- `COUNT`：同屏照片数量，改大更密集（如 `20`），改小更清爽（如 `8`）；
- `52+Math.random()*46`：照片尺寸范围，想更大就改成 `80+Math.random()*60`；
- `0.05+Math.random()*0.06`：上升速度，数字越大飞得越快。

---

## 三、更多自定义

| 想改什么 | 去哪里改 | 示例 |
| --- | --- | --- |
| 整体配色（背景） | `body{background:radial-gradient(...)}`（第 11 行） | 换成蓝色调：`#0a1a3a → #050b1a` |
| 爱心颜色 | `makeSprite(255,40,110)` 等（第 819~824 行） | 改成紫：`makeSprite(180,80,255)` |
| 爱心大小 | `SCALE=Math.min(W,H)*0.018`（第 788 行） | `0.018` 调大爱心变大 |
| 心跳速度 | `Math.sin(time*2.8)`（第 909 行） | 数字越大跳得越快 |
| 玫瑰数量 / 大小 | `for(let i=0;i<50;i++)`（第 751 行）、`20+Math.random()*26`（第 756 行） | 数量改 `30` |
| 玫瑰换成别的 emoji | `span.innerHTML="<b>🌹</b>"`（第 755 行） | `"<b>💖</b>"` |
| 星点数量 | `for(let i=0;i<140;i++)`（第 537 行） | 改 `200` 更密 |
| 点击爆裂数量 | `for(let i=0;i<120;i++)`（第 869 行） | 改 `200` 更炸 |
| 左侧代码滚动速度 | `scroll.style.animationDuration=(codeLines.length/10)+"s"`（第 531 行） | `/10` 改成 `/20` 更慢 |
| 跳过开场动画 | 把 `const steps=[...]` 换成 `const steps=[]` | 立即进入主画面 |
| 恢复蛇形光束 | 把 `=== 蛇形光束 ===` 整段的注释 `//` 去掉 | 多一层流动光效 |

---

## 四、发布到 GitHub Pages

1. 在 GitHub 新建仓库（如 `heartthrob`），本地执行：

```bash
git init
git add .
git commit -m "feat: 炫酷表白页面"
git branch -M main
git remote add origin https://github.com/<你的用户名>/heartthrob.git
git push -u origin main
```

2. 仓库 → **Settings** → **Pages** → **Build and deployment** → Source 选 `Deploy from a branch`，Branch 选 `main` / `root`，保存。

3. 等待 1~2 分钟，访问 `https://<你的用户名>.github.io/heartthrob/` 即可，把链接发给 TA 就行。

> 因为是纯静态页面，也可以直接丢到 Vercel / Netlify / Gitee Pages / 腾讯云 EdgeOne，拖文件夹上去即可。

---

## 五、开源相关建议

- 添加 `LICENSE`（推荐 MIT）：复制 [MIT 协议模板](https://opensource.org/license/mit)，把版权行改成你的名字和年份。
- 添加 `.gitignore`（可选）：本项目无依赖，主要用来忽略系统文件：

```
.DS_Store
Thumbs.db
```

- **注意隐私**：`img/` 里的照片会随仓库一起公开，上传前请确认对方肖像、隐私信息已获授权；不希望公开的话，可在 `.gitignore` 中加一行 `img/`，并在 README 中说明需自备图片。

---

## 常见问题

**Q：图片不显示？**
A：检查文件名是否为 `1.jpg ~ 7.jpg`、后缀大小写是否一致（Linux/服务器区分大小写），以及是否通过 `http://` 打开（部分浏览器对 `file://` 协议下的 canvas 取图有限制，用本地服务打开最稳）。

**Q：字显示不全 / 被截断？**
A：`sampleText()` 会自动缩小字号，若仍异常，减少单屏字数；超长中文建议拆成多屏。

**Q：手机上看很卡？**
A：把爱心粒子数 `const count=W*H>900000?2400:1600;`（第 838 行）调小，如 `1200/800`；照片 `COUNT` 调小到 `8`。

**Q：想加背景音乐？**
A：在 `<body>` 里加一行（浏览器可能拦截自动播放，建议保留 `controls`）：

```html
<audio src="music.mp3" autoplay loop controls></audio>
```

---

## License

[MIT](./LICENSE) © 2025
