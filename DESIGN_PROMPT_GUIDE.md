# 赛博晶透光影系统 (Cyber Luminescent & Optical Shading System) - 通用设计规范与提示词指南

> **定位说明**：本文档**剥离了具体业务组件（如方块、特定舞台、大屏等）**，纯粹提炼出本项目中这套**“高透发光、双层冷白激光倒角高光、双层指数高斯弥散光晕、人眼感知均衡色彩与呼吸光效”**的通用光影渲染系统。
> 无论您未来设计何种载体（如：3D 数据节点、拓扑图元、悬浮科技卡片、智慧建筑、机械构件、全息仪表盘等），只要应用本套光影提示词，即可呈现出完全一致的高级未来科技光影质感。

---

## 目录
1. [六大核心光影美学支柱（底层原理）](#1-六大核心光影美学支柱底层原理)
2. [AI 编程通用光影提示词（适用于任何 3D/Web 项目）](#2-ai-编程通用光影提示词适用于任何-3dweb-项目)
3. [AI 绘图纯光影提示词（Midjourney / SD / DALL-E）](#3-ai-绘图纯光影提示词midjourney--sd--dall-e)
4. [Three.js / WebGL 通用光影 Shader & 材质参数模版](#4-threejs--webgl-通用光影-shader--材质参数模版)
5. [光影色彩感知补偿规范（色彩代号速查）](#5-光影色彩感知补偿规范色彩代号速查)

---

## 1. 六大核心光影美学支柱（底层原理）

无论应用在什么几何形体上，本套光影效果均由以下 **6 个纯光学图层** 复合而成：

```
                 [图层 5: 纯白冷光倒角高光 (Additive Diamond White Glint)]
                                     ↓
[图层 4: 饱和霓虹棱角线] → [ 3D 实体/卡片: 图层 1 高透晶莹玻璃外壳 (High Shininess) ]
                                     ↑
                   [图层 2: 内部自发光能量核 (Additive Core Pulse)]
                                     │
           ──────────────────────────┴────────────────────────── (承载基面)
             [图层 3A: 内层高斯聚焦强光晕 (1.8x, exp(-d²*2.6), Opacity: 0.7~1.0)]
             [图层 3B: 外层高斯大气弥散光雾 (3.6x, exp(-d²*2.6), Opacity: 0.4~0.6)]
```

1. **高折射通透镜面质感（Ultra-Luminescent Translucent Shell）**：
   - 彻底拒绝暗淡发灰的塑料感；通过高镜面聚集度（`shininess: 240+`）、纯白高光（`specular: #ffffff`）与基础自发光（`emissive: 0.40+`）打造出高能量通透感。
2. **双层锐利冷白激光倒角高光（Dual-Layer Diamond Laser Bevels）**：
   - **底层**：饱和主色激光线条（`opacity: 0.85`）；
   - **表层**：微距外扩（`scale: 1.004`）的纯净冷白钻石高光（`#ffffff`，加法混合，`opacity: 0.65 ~ 1.0`），在所有棱角交汇处产生极具未来感的锋利高光折射。
3. **双层同心指数高斯漫散光晕（Dual Concentric Gaussian Halos - $\exp(-d^2 \cdot 2.6)$）**：
   - 无硬边、无锯齿的平滑能量场：
     - **内层核心光晕**：小范围强聚光（1.5~2.0x 尺寸），提供厚重能量基底；
     - **外层大气光晕**：大范围向外柔和漫散（3.5~4.0x 尺寸），形成梦幻悬浮光雾。
4. **内部加法律动晶核（Additive Pulsing Core）**：
   - 几何体内部嵌套高亮加法混合内核，产生由内而外透光的“能量心脏”感。
5. **人眼色彩感知平衡补偿（Photopic Luminance Balancing）**：
   - 消除人眼对不同颜色亮度的感知差异：对青蓝（天然高亮）采用标准增益，对金色给予 `+30%` 自发光增益，对红色给予 `+40%` 自发光增益，确保所有颜色在悬停/高亮时拥有同等炽烈的视觉冲击力。
6. **对数深度抗共面撕裂（Zero Z-Fighting Precision Pipeline）**：
   - 采用 `logarithmicDepthBuffer`、`camera.near = 0.8` 以及 `polygonOffset`，保证细微高光棱线与光晕在移动端与远景下 100% 纯净无抖动。

---

## 2. AI 编程通用光影提示词（适用于任何 3D/Web 项目）

当您需要为**任何 3D 物体、数据图元、卡片、机械模型或场景**赋予这套光影时，只需将以下提示词加入您的需求中：

```markdown
【光影效果与渲染质感要求 - 赛博晶透全息光影体系】：
请为本项目的所有 3D 构件/视觉图元应用以下标准光影与材质体系：

1. 材质与高光（Material & Specular）：
   - 采用高透光亮晶体质感（MeshPhongMaterial），shininess 设为 240 以上，specular 设为纯白 #ffffff，基础 emissiveIntensity 保持在 0.40~0.60，保证形体通体晶亮通透、绝不发暗发灰；
2. 棱角激光倒角高光（Dual-Layer Laser Edge Glints）：
   - 所有几何形体外边缘采用双层线框系统：第一层为主色高饱和霓虹线（opacity: 0.85），第二层为微距外扩（scale: 1.004）的纯冷白钻石激光高光线（#ffffff，加法混合 AdditiveBlending，opacity: 0.70~1.0），形成极度锋利璀璨的倒角折射；
3. 底部/背景双层高斯光晕（Dual Concentric Gaussian Glow Halos）：
   - 在图元下方或背景投影双层程序化指数高斯衰减光晕（exp(-d^2 * 2.6)），内层为 1.8 倍尺寸的高亮核心聚光，外层为 3.6 倍尺寸的柔和弥散光雾，加法混合（AdditiveBlending），禁用深度写入（depthWrite: false）；
4. 内部能量晶核与呼吸律动：
   - 内部嵌套加法发光内核，配合非线性正弦函数（Math.pow(breath, 1.8)）实现边缘变亮、晶核膨胀（呼大吸小）、光晕向外扩张的有机呼吸动效；
5. 感知色彩亮度平衡：
   - 正常态采用荧光青蓝（#00f0ff / #48f8ff）；
   - 预警态采用耀眼金黄（#ffb808 / #ffea54，发光强度增益 1.75）；
   - 危险/高危态采用极光红宝石（#ff1a46 / #ff6686，发光强度增益 1.90）；
6. 移动端抗闪烁渲染管线：
   - 开启 logarithmicDepthBuffer: true，摄像机 near 设为 0.8，实体材质开启 polygonOffset: true，防止任何线面共面闪烁。
```

---

## 3. AI 绘图纯光影提示词（Midjourney / SD / DALL-E）

无论绘制任何主题（如：智能汽车、机器义体、全息图表、太空舱室、数据球体等），只需在主体描述后添加这组**纯光影修饰语**：

### 纯光影通用后缀修饰词 (Lighting Modifier Tag)
> **English (可直接拼接到任何物体后面)**:  
> `..., glowing translucent crystal-glass material, sharp diamond-white laser bevel edge glints, dual-layer edge highlights, internal radiant pulsating energy core, soft exponential Gaussian neon glow halo pooling beneath on dark titanium surface, hyper-saturated cyan and amber gold and ruby red luminous color accents, high refractive dispersion, caustic specular reflections, volumetric sci-fi bloom, octane render, Unreal Engine 5 cinematic ray-tracing lighting, 8k, ultra-sharp details`

### 示例应用 A：应用于【全息科技卡片 / 仪表盘】
> `A set of floating futuristic tech UI cards and holographic widgets, glowing translucent crystal-glass material, sharp diamond-white laser bevel edge glints, soft exponential Gaussian neon glow halos pooling around the dark titanium background, glowing cyan and amber telemetry graphics, octane render, ray tracing, 8k --ar 16:9 --v 6.0`

### 示例应用 B：应用于【球体/核心数据节点】
> `A futuristic floating cybernetic energy sphere node, glowing translucent crystal-glass shell, sharp diamond-white laser bevel highlights, pulsing radiant glowing core inside, soft Gaussian neon light fog diffusion underneath, dark obsidian background with subtle grid, 8k --ar 1:1 --v 6.0`

---

## 4. Three.js / WebGL 通用光影 Shader & 材质参数模版

如果您需要在代码中直接复用这套纯光影材质系统，以下是直接可用的模块化参数代码：

```javascript
// 1. 高透亮水晶外壳材质
function createLuminescentMaterial(baseColor, glowColor) {
  return new THREE.MeshPhongMaterial({
    color: baseColor,
    emissive: glowColor,
    emissiveIntensity: 0.42,  // 基础常亮度（悬停时可动态升至 1.35 ~ 1.90）
    transparent: true,
    opacity: 0.64,
    shininess: 240,           // 极致凝聚的镜面高光点
    specular: 0xffffff,       // 纯白反射
    depthWrite: true,
    polygonOffset: true,      // 消除与棱线共面冲突
    polygonOffsetFactor: 1.0,
    polygonOffsetUnits: 1.0
  });
}

// 2. 双层锐利冷白激光倒角线
function createBevelLaserEdges(geometry, glowColor) {
  const group = new THREE.Group();
  const edgesGeo = new THREE.EdgesGeometry(geometry);

  // 第一层：高饱和主色霓虹线
  const neonMat = new THREE.LineBasicMaterial({
    color: glowColor,
    transparent: true,
    opacity: 0.90,
    depthWrite: false
  });
  group.add(new THREE.LineSegments(edgesGeo, neonMat));

  // 第二层：钻石冷白激光倒角高光 (微距外扩 + 加法混合)
  const whiteMat = new THREE.LineBasicMaterial({
    color: 0xffffff,
    transparent: true,
    opacity: 0.85,
    blending: THREE.AdditiveBlending,
    depthWrite: false
  });
  const whiteLines = new THREE.LineSegments(edgesGeo, whiteMat);
  whiteLines.scale.set(1.004, 1.004, 1.004);
  group.add(whiteLines);

  return group;
}

// 3. 高精 256x256 指数高斯光晕纹理生成器
function createGaussianGlowTexture() {
  const canvas = document.createElement('canvas');
  canvas.width = 256;
  canvas.height = 256;
  const ctx = canvas.getContext('2d');
  const grad = ctx.createRadialGradient(128, 128, 0, 128, 128, 128);
  grad.addColorStop(0.0, 'rgba(255, 255, 255, 1.0)');
  grad.addColorStop(0.16, 'rgba(255, 255, 255, 0.94)');
  grad.addColorStop(0.36, 'rgba(255, 255, 255, 0.70)');
  grad.addColorStop(0.58, 'rgba(255, 255, 255, 0.36)');
  grad.addColorStop(0.78, 'rgba(255, 255, 255, 0.12)');
  grad.addColorStop(1.0, 'rgba(255, 255, 255, 0.0)');
  ctx.fillStyle = grad;
  ctx.fillRect(0, 0, 256, 256);
  const tex = new THREE.CanvasTexture(canvas);
  tex.needsUpdate = true;
  return tex;
}
```

---

## 5. 光影色彩感知补偿规范（色彩代号速查）

| 状态语义 | 基底暗色 (Base) | 发射光 (Glow) | 内核能量色 (Core) | 激光边缘色 (Laser) | 倒角白光 (Glint) | 悬停发光倍率 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **正常态 (青蓝)** | `#063450` | `#00f0ff` | `#48f8ff` | `#86ffff` | `#ffffff` | **1.35x** |
| **预警态 (炽金)** | `#582c02` | `#ffb808` | `#ffea54` | `#fff498` | `#fffcf0` | **1.75x** (+30% 增益) |
| **高危态 (红宝石)** | `#5e0412` | `#ff1a46` | `#ff6686` | `#ffa4ba` | `#fff2f6` | **1.90x** (+40% 增益) |
