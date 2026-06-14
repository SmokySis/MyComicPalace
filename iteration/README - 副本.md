# 漫评记录阁
进入漫画长草期之后就想着该总结整理一下自己看过的百来部漫画了，做一图流改起来比较麻烦，干脆让AI帮写了个网站。

### 一、 怎么导入图片、增减作品？

拉到 `index.html` 的**最底部**，你会看到 `<script>` 标签里有一段被称为“数据源”的代码：

```javascript
// 核心数据源：你想展示的漫画都在这里
const data = [
  { title: "电锯人", sub: "热血", rating: 4, cover: "images/chainsaw.jpg", desc: "节奏怪异且极猛..." },
  { title: "海贼王", sub: "冒险", rating: 5, cover: "https://example.com/op.jpg", desc: "连载数十载的史诗巨作..." }
];

```

#### 1. 怎么导入封面图片 (`cover`)？

你有两种方式来放入图片：

* **本地图片（推荐，最稳定）**：
在你的 `index.html` 文件的**同级目录下**，新建一个名为 `images` 的文件夹。把下载好的漫画封面（比如 `op.jpg`）放进去。然后在代码里写：
`cover: "images/op.jpg"`
* **网络图片**：
如果你在网上看到了某张漫画封面的链接，直接把网址复制进去即可：
`cover: "https://img.moe/anime/123.jpg"`

#### 2. 怎么减少作品？

直接用鼠标选中不想要的那一行（从 `{` 开始到 `},` 结束），直接按 `Delete` 键删掉即可。

#### 3. 怎么增加作品？

复制其中一行，在英文逗号后面回车换行，粘贴并修改文字即可。例如：

```javascript
{ title: "新漫画名字", sub: "分类名", rating: 5, cover: "图片路径", desc: "这是我的自定义点评文字" },

```

*注意：每个大括号 `{}` 之间一定要用英文逗号 `,` 隔开。*

---

### 二、 告别“全员彩色抖动”：如何自由控制品评文字效果

为了实现“不同作品、不同特效，还能改字号和颜色”，我们需要对代码做一点点小升级。请按以下三步操作：

#### 第一步：升级 CSS 样式表（控制外观、字号、颜色）

在代码上方的 `<style>` 标签里，找到原本的 `.desc` 样式，将其**替换**为以下代码：

```css
/* 1. 默认点评文字：普通的、安静的文字 */
.desc {
  margin: 0;
  font-size: 16px;       /* 💡【在这里改字号】想变大就改 16px 或 18px */
  line-height: 1.6;
  text-align: left;
  color: #554433;        /* 💡【在这里改默认静止文字的颜色】 */
  display: block;
  transition: all 0.3s;
}

/* 2. 特效外挂包 A：既彩虹变色，又疯狂抖动 */
.desc.rainbow-shake {
  font-weight: bold;
  animation: rainbow 3s linear infinite, textShake 0.15s linear infinite;
}

/* 3. 特效外挂包 B：只有彩虹变色，安静不抖动 */
.desc.rainbow-only {
  font-weight: bold;
  animation: rainbow 3s linear infinite;
}

/* 4. 特效外挂包 C：只有抖动，但是固定单一颜色（比如刺激的纯红色） */
.desc.shake-only {
  color: #ff4757;        /* 💡【在这里改单色抖动的颜色】 */
  animation: textShake 0.15s linear infinite;
}

```

#### 第二步：升级 JS 数据源（给作品派发特效）

回到最底部的 `data` 数组，在每个漫画里面多加一个属性：`effect`。

```javascript
const data = [
  { title: "电锯人", sub: "热血", rating: 4, cover: "", desc: "节奏怪异且极猛...", effect: "rainbow-shake" }, // 既变色又抖
  { title: "海贼王", sub: "冒险", rating: 5, cover: "", desc: "连载数十载...", effect: "rainbow-only" },  // 只变色不抖
  { title: "灌篮高手", sub: "运动", rating: 5, cover: "", desc: "属于青春与汗水...", effect: "" },           // 默认静止普通字
  { title: "间谍过家家", sub: "喜剧", rating: 4, cover: "", desc: "顶尖特工...", effect: "shake-only" }    // 只抖动，固定红色
];

```

*💡 提示：如果 `effect` 留空 `""`，它就是最普通的静止文字！*

#### 第三步：修改页面渲染规则

在 JS 代码中找到 `function renderCards()` 这一段，往下看几行，找到渲染文字的这一行：

```javascript
<p class="desc">${item.desc}</p>

```

把它**修改**为：

```javascript
<p class="desc ${item.effect || ''}">${item.desc}</p>

```

这一步的意思是：让网页自动去读你在第二步里写好的 `effect` 标签，并自动给他穿上对应的“特效外衣”。

---

### 三、 进阶：如何微调动画的细节？

如果你想把彩虹色换成别的颜色，或者觉得抖动太快、太大，可以调节这两个“动画说明书”（`@keyframes`）：

#### 1. 想自定义彩虹变色的颜色？

找到 `@keyframes rainbow`：

```css
@keyframes rainbow {
  0% { color: #ff7979; }   /* 刚开始的颜色 */
  25% { color: #badc58; }  /* 1/4 时间处的颜色 */
  50% { color: #7ed6df; }  /* 中间的颜色 */
  75% { color: #e056fd; }  /* 3/4 时间处的颜色 */
  100% { color: #ff7979; } /* 结束时的颜色（通常和0%一致，保证丝滑循环） */
}

```

你可以把里面的 `#ff7979` 等十六进制颜色代码，换成任意你喜欢的颜色。

#### 2. 想改变抖动的剧烈程度？

找到 `@keyframes textShake`：

```css
@keyframes textShake {
  0% { transform: translate(0, 0); }
  20% { transform: translate(-0.6px, 0.4px); } /* 💡这里的 0.6px 和 0.4px 是位移距离 */
  40% { transform: translate(-0.4px, -0.6px); }
  ...
}

```

* 如果你想让它**抖得更疯狂**：把里面的 `0.6px`、`0.4px` 改大，比如改为 `2px`、`-3px`。
* 如果你想让它**变慢一点**：在上面的 CSS 样式里，把 `textShake 0.15s` 改大，比如改为 `0.3s`，它就会变成鬼畜慢动作抖动。

按照这个逻辑，你就可以完全自由地掌控整个网站的每一处细节了！赶快打开代码亲自动手试一下吧！