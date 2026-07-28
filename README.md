# 🌍 真实旋转地球（3D 可视化）

一个基于 WebGL / [Three.js](https://threejs.org) 的高逼真 3D 地球，支持鼠标/触摸交互，带真实卫星贴图、昼夜城市灯光、云层、大气辉光、四类卫星轨道、月球公转与全球热门旅游城市标注。

**完全离线、单文件即可运行** —— 双击 `earth-standalone.html` 就能看到。

---

## ✨ 功能特性

| 类别 | 内容 |
|------|------|
| **地球本体** | NASA 卫星日面贴图、地形法线（山脉凹凸）、海洋高光、23.5° 地轴倾角 |
| **昼夜效果** | 自定义 Shader，背光面自动亮起真实城市灯火 |
| **云层** | 半透明云层，自转速度略快于地表 |
| **大气** | Fresnel 边缘蓝光，模拟大气层散射辉光 |
| **星空** | 4000 颗程序生成的背景星 |
| **卫星轨道** | 近地卫星（LEO）· 极地卫星 · 同步卫星（GEO）· 大椭圆轨道，均按**开普勒运动**（近地点快、远地点慢）|
| **月球** | 带真实月面贴图，绕地公转 |
| **城市标注** | 12 座全球热门旅游城市，脉冲光环 + 名称标签，**背面自动隐藏** |

### 标注的城市
巴黎 · 伦敦 · 罗马 · 巴塞罗那 · 伊斯坦布尔 · 迪拜 · 纽约 · 东京 · 北京 · 曼谷 · 新加坡 · 悉尼

---

## 🚀 快速开始

### 方式一：单文件版（推荐，随处可用）
直接双击打开：

```
earth-standalone.html
```

一个文件包含全部依赖（引擎 + 贴图 + 逻辑），无需网络、无需服务器、无需其它文件。可随意拷贝到 U 盘或发送给他人。

### 方式二：开发版（便于修改）
打开 `earth.html`（依赖同目录的 `libs/` 与 `assets/`）。

> ⚠️ **注意**：`earth.html` 若通过 `file://` 双击打开可能出现「黑球」——因为浏览器把本地图片当跨域资源，WebGL 拒绝加载。它已内置 Base64 回退（`libs/textures.js`）来规避此问题；若仍异常，请改用**方式一**，或用本地服务器打开（见下）。

### 用本地服务器打开（可选）
```bash
# 任选其一，在项目目录执行
npx http-server .        # Node
python -m http.server    # Python
```
然后浏览器访问 `http://localhost:8080/earth.html`。

---

## 🎮 操作方式

| 操作 | 效果 |
|------|------|
| 拖动 / 单指滑动 | 旋转视角 |
| 滚轮 / 双指捏合 | 缩放（1.4 ~ 45 倍地球半径）|
| 自动 | 地球自转、卫星运行、月球公转 |

---

## 📁 文件结构

```
.
├── earth-standalone.html   # 🚀 单文件版（一切内联，到处可跑）
├── earth.html              # 开发版（引用下方 libs/ 与 assets/）
├── libs/                   # JS 库
│   ├── three.min.js            # Three.js r128 引擎
│   ├── OrbitControls.js        # 相机交互控制
│   ├── CSS2DRenderer.js        # 城市名 HTML 标签渲染
│   └── textures.js             # 6 张贴图的 Base64 内嵌数据
├── assets/                 # 原始贴图
│   ├── earth_atmos_2048.jpg    # 日面卫星图
│   ├── earth_normal_2048.jpg   # 地形法线
│   ├── earth_specular_2048.jpg # 海洋高光
│   ├── earth_lights_2048.png   # 夜间城市灯光
│   ├── earth_clouds_1024.png   # 云层
│   └── moon_1024.jpg           # 月面
└── README.md
```

---

## 🔧 重新打包单文件版

修改 `earth.html` 后，用以下命令重新生成 `earth-standalone.html`（需 Node.js）：

```bash
node -e '
const fs=require("fs");
let html=fs.readFileSync("earth.html","utf8");
const libs=["libs/three.min.js","libs/OrbitControls.js","libs/CSS2DRenderer.js","libs/textures.js"];
for(const p of libs){
  let code=fs.readFileSync(p,"utf8").replace(/<\/script>/g,"<\\/script>");
  html=html.replace(`<script src="${p}"></script>`,`<script>\n${code}\n</script>`);
}
fs.writeFileSync("earth-standalone.html",html);
console.log("done:",(fs.statSync("earth-standalone.html").size/1048576).toFixed(2),"MB");
'
```

若更换了 `assets/` 里的贴图，需先重新生成 `libs/textures.js`（把图片转 Base64），再执行上面的打包命令。

---

## 📱 在手机 / 微信中打开

微信打开的是**网址（URL）**而非本地文件，因此需先托管到 HTTPS：

1. 将 `earth-standalone.html` 上传到静态托管（Gitee Pages / GitHub Pages / 腾讯云 COS / Netlify 等）。
2. 拿到 `https://...` 链接，发到微信点开即可（微信内置浏览器支持 WebGL）。
3. iOS 上若不流畅，点右上角「⋯ → 在浏览器打开」。

> 直接把 `.html` 文件发到聊天窗口**不会**渲染，必须用链接。

---

## 🛠️ 技术栈

- **Three.js r128** — WebGL 渲染引擎
- **OrbitControls** — 相机交互
- **CSS2DRenderer** — HTML 城市标签叠加
- 自定义 **GLSL Shader** — 昼夜混合、城市灯光、大气辉光
- **开普勒方程** — 卫星/月球真实轨道运动

## 📄 贴图来源与许可

贴图来自 Three.js 官方示例（`threejs.org/examples/textures/planets/`），原始数据源为 NASA。Three.js 采用 MIT 许可。本项目仅供学习与演示使用。

## ⚠️ 数据说明

轨道尺度已做适度可视化压缩（尤其月球，真实距离约 60 倍地球半径），并非严格比例；卫星运动遵循开普勒规律但为观赏做了时间加速。
