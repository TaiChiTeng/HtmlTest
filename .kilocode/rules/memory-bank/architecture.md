# 系统架构

## 项目结构
```
HtmlTest/
├── 游戏类文件
│   ├── Helltaker5050.html      # Helltaker 游戏完整版（带音效）
│   ├── HelltakerHalf.html       # Helltaker 9关版
│   ├── HelltakerX.html          # Helltaker 优化版
│   ├── MagicCube3D.html         # 3D 魔方（Three.js）
│   ├── MagicCube3DSmooth.html  # 3D 魔方（物理力矩修正版）
│   ├── MagicCube2D.html         # 2D 魔方
│   ├── MagicCube2x2x2.html       # 2x2 魔方
│   ├── sudoku.html               # 数独游戏
│   ├── Five.html                 # 五子棋
│   ├── BalloonGame.html          # 气球爆破挑战
│   ├── BalloonGameMobile.html    # 气球游戏（移动端）
│   ├── BalloonScore.html         # 气球计分版
│   ├── BalloonWithNum.html       # 带数字气球
│   ├── Ball.html                 # 物理方块弹射（Matter.js）
│   ├── garbageSorting.html        # 垃圾分类游戏
│   └── 7.7.html                 # 老虎机
├── 动画类文件
│   ├── Balloon.html              # 彩色气球动画
│   ├── Star.html                 # 星空动画
│   ├── Galaxy.html                # 银河星空
│   ├── StarTraceMouse.html        # 星空鼠标互动
│   ├── Snow.html                  # 雪花飘落
│   ├── Rain.html                  # 下雨效果
│   ├── Vortex.html                # 旋涡星空
│   ├── Matrix01.html              # Matrix 数字雨
│   ├── Matrix01v2.html            # Matrix 球面
│   ├── Matrix01v3.html            # Matrix 数字太极
│   ├── ILU.html                  # 爱心动画
│   └── ILUv2.html                # 爱心动画 v2
└── .kilocode/
    └── rules/
        └── memory-bank/  # Memory Bank 文档
```

## 关键技术决策

### 1. 独立文件架构
- 每个游戏/动画都是独立的 HTML 文件
- 包含所有必要的 HTML、CSS 和 JavaScript
- 无需构建工具或依赖管理

### 2. 渲染技术选择
- **2D 游戏和动画**: 使用 Canvas 2D API
- **3D 游戏**: 使用 Three.js WebGL 渲染
- **物理模拟**: 使用 Matter.js 物理引擎
- **UI 组件**: 使用 Tailwind CSS 快速开发

### 3. 响应式设计
- 使用 `viewport` meta 标签适配移动端
- 弹性布局和相对定位
- 触控事件支持 (`touchstart`, `touchmove`, `touchend`)

### 4. 音频系统
- Helltaker5050 使用 Web Audio API 程序化生成音效
- 无需外部音频文件
- 支持 BGM 和 SFX 分离控制

## 设计模式

### 1. 游戏循环模式
```javascript
function loop(timestamp) {
    let dt = (timestamp - lastTime) / 1000;
    update(dt);
    draw();
    requestAnimationFrame(loop);
}
```

### 2. 实体组件模式
```javascript
function createEntity(x, y, type) {
    return { x, y, vx: x, vy: y, type, dir: -1, dead: false };
}
```

### 3. 状态管理模式
- 使用全局 `game` 对象管理游戏状态
- 状态包括: `PLAYING`, `WON`, `LOST`

### 4. 粒子系统
```javascript
function spawnParticle(x, y, color, type) {
    game.particles.push({
        x, y, 
        vx: (Math.random() - 0.5) * 0.1,
        vy: (Math.random() - 1) * 0.1,
        color, life: 1.0, size: Math.random() * 2 + 1, type
    });
}
```

## 组件关系

### Helltaker 游戏架构
```
Level Data (关卡配置)
    ↓
Game Engine (游戏引擎)
    ├─ Input Handler (输入处理)
    ├─ Physics Update (物理更新)
    ├─ Collision Detection (碰撞检测)
    └─ Rendering (渲染)
        ├─ Canvas 2D
        └─ Particle System (粒子系统)
```

### 3D 魔方架构
```
Three.js Scene
    ├─ Camera (PerspectiveCamera)
    ├─ Controls (OrbitControls)
    ├─ Lighting (Ambient + Directional)
    └─ Cube Meshes (27个小方块)
        └─ Materials (6面颜色)
```

### 数独游戏架构
```
Game State
    ├─ Board (9x9 网格)
    ├─ Solution (完整解)
    ├─ Puzzle (当前谜题)
    └─ Timer & Score
```

## 关键实现路径

### Helltaker 关卡加载流程
1. `loadLevel(idx)` → 选择关卡
2. `initPuzzle(lvl)` 或 `initBoss(lvl)` → 初始化
3. 解析地图数据 → 生成网格
4. 创建实体（玩家、女孩、岩石、骷髅等）
5. 更新 UI 显示

### 物理方块弹射流程
1. `init()` → 创建 Matter.js 引擎
2. `createLevel()` → 生成方块堆
3. `resetBird()` → 创建弹射小球
4. `handleCollision()` → 碰撞检测和消除
5. `checkElimination()` → 颜色匹配判断

### 数独生成算法
1. `generateSolution()` → 回溯法生成完整解
2. `removeNumbers()` → 根据难度移除数字
3. `isValid()` → 验证数字放置合法性
4. `checkWin()` → 检查游戏完成

## 外部依赖

### CDN 资源
- **Three.js**: `https://unpkg.com/three@0.160.0/build/three.module.js`
- **Matter.js**: `https://cdnjs.cloudflare.com/ajax/libs/matter-js/0.19.0/matter.min.js`
- **Tailwind CSS**: `https://cdn.tailwindcss.com`
- **Font Awesome**: `https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.7.2/css/all.min.css`
- **Google Fonts**: Crimson Text, Inter

## 性能优化

1. **requestAnimationFrame**: 使用浏览器优化的动画循环
2. **Canvas 渲染**: 批量绘制减少 DOM 操作
3. **对象池**: 复用粒子对象减少 GC
4. **事件节流**: 防止频繁触发的事件
5. **离屏 Canvas**: 预渲染复杂图形

## 浏览器兼容性

- **现代浏览器**: Chrome, Firefox, Safari, Edge
- **移动端**: iOS Safari, Chrome Mobile
- **最低要求**: 支持 ES6 和 Canvas 2D API
