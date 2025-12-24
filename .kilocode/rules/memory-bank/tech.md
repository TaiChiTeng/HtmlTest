# 技术文档

## 技术栈

### 核心技术
- **HTML5**: 语义化标签、Canvas、Audio API
- **CSS3**: Flexbox、Grid、动画、媒体查询
- **JavaScript (ES6+)**: 箭头函数、类、模块、Promise、async/await

### 第三方库
- **Three.js** (v0.160.0): 3D 渲染引擎
  - 用于 MagicCube3D 系列
  - 提供 WebGL 渲染、场景管理、相机控制
  - 模块化导入: `import * as THREE from '...'`

- **Matter.js** (v0.19.0): 2D 物理引擎
  - 用于 Ball.html 物理模拟
  - 提供刚体物理、碰撞检测、约束系统
  - 全局对象: `Matter.Engine`, `Matter.World`, `Matter.Bodies`

- **Tailwind CSS**: 实用优先的 CSS 框架
  - 用于 sudoku.html, garbageSorting.html, Five.html
  - CDN 实时编译
  - 响应式设计工具类

- **Font Awesome** (v6.7.2): 图标库
  - 用于游戏 UI 图标
  - 通过 CDN 引入

### 字体
- **Crimson Text**: Helltaker 游戏字体
- **Inter**: 其他游戏默认字体
- **Google Fonts**: 字体托管服务

## 开发环境

### 运行方式
- **本地开发**: 直接在浏览器中打开 HTML 文件
- **无需构建**: 所有资源通过 CDN 加载
- **无需服务器**: 可使用 `file://` 协议打开

### 推荐工具
- **VS Code**: 代码编辑器
- **Chrome DevTools**: 调试工具
- **Live Server**: 可选的本地开发服务器

## 技术约束

### 浏览器要求
- 支持 ES6 语法
- 支持 Canvas 2D API
- 支持 WebGL (仅 3D 游戏)
- 支持 Web Audio API (仅 Helltaker5050)

### 性能约束
- 目标帧率: 60 FPS
- 最大粒子数: ~500 个 (Helltaker5050)
- 最大实体数: ~100 个 (大部分游戏)

### 文件大小约束
- 单个 HTML 文件建议 < 2000 行
- 内联 CSS/JS 保持简洁
- 使用 CDN 减少本地资源

## 依赖管理

### CDN 资源列表
```html
<!-- Three.js -->
<script type="module">
  import * as THREE from 'https://unpkg.com/three@0.160.0/build/three.module.js';
</script>

<!-- Matter.js -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/matter-js/0.19.0/matter.min.js"></script>

<!-- Tailwind CSS -->
<script src="https://cdn.tailwindcss.com"></script>

<!-- Font Awesome -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.7.2/css/all.min.css">

<!-- Google Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Crimson+Text:wght@400;600&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
```

## 工具使用模式

### Canvas 2D 渲染模式
```javascript
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    // 绘制内容
    requestAnimationFrame(draw);
}
```

### Three.js 场景设置模式
```javascript
import * as THREE from '...';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, w/h, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({ antialias: true });

renderer.setSize(w, h);
document.body.appendChild(renderer.domElement);

function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
}
```

### Matter.js 物理引擎模式
```javascript
const engine = Matter.Engine.create();
const world = engine.world;

const box = Matter.Bodies.rectangle(x, y, w, h);
Matter.World.add(world, box);

Matter.Engine.run(engine);
```

### 响应式设计模式
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
```

```javascript
// 触控事件处理
canvas.addEventListener('touchstart', handleTouchStart);
canvas.addEventListener('touchmove', handleTouchMove);
canvas.addEventListener('touchend', handleTouchEnd);
```

## 代码规范

### 命名约定
- **变量**: camelCase (如 `gameState`, `playerX`)
- **常量**: UPPER_SNAKE_CASE (如 `MAX_PARTICLES`, `GRID_SIZE`)
- **函数**: camelCase (如 `drawGame()`, `updatePhysics()`)
- **类**: PascalCase (如 `AudioManager`, `GameEngine`)

### 文件结构
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="...">
    <title>游戏名称</title>
    <!-- 外部资源 -->
</head>
<body>
    <!-- HTML 结构 -->
    <canvas id="gameCanvas"></canvas>
    
    <!-- 内联样式 -->
    <style>
        /* CSS */
    </style>
    
    <!-- 脚本 -->
    <script>
        // 游戏逻辑
    </script>
</body>
</html>
```

### 注释规范
```javascript
// 单行注释

/**
 * 多行注释
 * 描述函数用途
 */
function functionName() {
    // 实现
}
```

## 调试技巧

### Canvas 调试
- 使用 `console.log()` 输出状态
- 绘制调试信息到 Canvas
- 使用 Chrome DevTools Performance 面板

### Three.js 调试
- 使用 `THREE.GridHelper` 显示网格
- 使用 `THREE.AxesHelper` 显示坐标轴
- 检查渲染统计: `renderer.info`

### 性能分析
```javascript
let lastTime = 0;
let frameCount = 0;

function loop(timestamp) {
    frameCount++;
    if (timestamp - lastTime >= 1000) {
        console.log(`FPS: ${frameCount}`);
        frameCount = 0;
        lastTime = timestamp;
    }
    requestAnimationFrame(loop);
}
```

## 常见问题解决

### Canvas 模糊问题
```javascript
function resizeCanvas() {
    const dpr = window.devicePixelRatio || 1;
    canvas.width = canvas.clientWidth * dpr;
    canvas.height = canvas.clientHeight * dpr;
    ctx.scale(dpr, dpr);
}
```

### 移动端触控延迟
```javascript
canvas.addEventListener('touchstart', (e) => {
    e.preventDefault(); // 防止默认行为
    // 处理触控
}, { passive: false });
```

### 音频自动播放限制
```javascript
// 需要用户交互后才能播放
document.addEventListener('click', () => {
    audioContext.resume();
}, { once: true });
```

## 未来扩展建议

### 可能的技术升级
- 使用 Web Workers 进行后台计算
- 使用 IndexedDB 存储游戏进度
- 使用 WebRTC 实现多人游戏
- 使用 WebGL 2 提升渲染性能
- 使用 WebAssembly 优化性能密集型计算

### 新功能方向
- 添加游戏排行榜
- 实现关卡编辑器
- 支持游戏录制和回放
- 添加成就系统
- 实现云存档功能
