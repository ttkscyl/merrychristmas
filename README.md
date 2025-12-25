提示词：

角色设定：
你是一位世界级的Programmer，精通 Three.js (WebGL)、GLSL 着色器逻辑， Google MediaPipe 计算机视觉集成，HTML代码以及JavaScript。

任务目标：
请编写一个项目，该项目必须包含完整的 HTML 结构、CSS 样式和基于 ES Module 的 JavaScript 代码。

详细开发规格说明书
1. UI 设计与 CSS 样式 
字体： 引入 Google Fonts 'Cinzel' (标题) 和 'Times New Roman' (正文)。
配色： 背景 #000000。主色调：香槟金，奶油白。

UI 组件：
Loader： 黑色全屏遮罩，中间为 40px 的金色旋转 Spinner (border-top: 1px solid #d4af37)，下方文字 "请稍等片刻"。初始化完成后淡出。
标题： 顶部居中 <h1> "Merry Christmas"，字号 54px，使用 CSS linear-gradient (白到金) 实现文字渐变色，并加辉光阴影。
Webcam： 在右下角创建一个容器 ，包含 <video> 和 160x120 的 <canvas> 用于 CV 推理。
说明框：。在左上角添加，有金色outline，黑色背景。内容如下：
手势交互说明
👊握拳 → 树形模式
✋ 张开手 → 散开模式
🤏 捏合 → 聚焦照片

2. Three.js 场景构建 (Scene Setup)
渲染器： WebGLRenderer (antialias: true)，toneMapping 设置为 ReinhardToneMapping (曝光 2.2)。
相机： 透视相机，位置 (0, 2, 50)。
环境 (Environment)： 使用 RoomEnvironment 配合 PMREMGenerator 生成高反射金属环境贴图。
后期处理 (Post-Processing)：
使用 EffectComposer。
添加 UnrealBloomPass (辉光特效)。参数为： resolution (window size), strength: 0.45, radius: 0.4, threshold: 0.7。
灯光系统：
AmbientLight (0.6)。
内部 PointLight (橙色, 强度 2)。
SpotLight (金色, 强度 1200, 位置 30,40,40)。
SpotLight (蓝色, 强度 600, 位置 -30,20,-30) 用于冷暖对比。

3. 粒子系统与内容 (Content Generation)
屏幕中心有一个圣诞树，静止状态下，持续自转。
圣诞树顶部是一个星星，发光。

圣诞树外层由三角体粒子包裹。
内层由几何体混合。

粒子总数： 约 1500 个主体粒子 + 2500 个尘埃粒子。
几何体混合：
BoxGeometry: 材质为金色和深绿色 MeshStandardMaterial。
SphereGeometry: 材质为金色和红色 MeshPhysicalMaterial (红色带 clearcoat)。
Candy Cane (糖果手杖): 使用 CatmullRomCurve3 生成弯钩路径，配合 TubeGeometry。必须程序化生成纹理：使用 Canvas 2D API 绘制白底红斜纹，创建 CanvasTexture。
照片墙功能：
生成一张Canvas 纹理照片, 图片调用同目录里的图片文件，叫：photo.jpg。
照片必须包裹在金色的相框 (BoxGeometry) 中。

4. 状态机与动画逻辑 (State Machine & Logic)
定义全局 STATE 对象，包含模式 (mode) 和手势数据。在 animate 循环中根据模式计算目标位置并使用 lerp 平滑过渡。
Mode 1: TREE (树模式)
粒子排列成螺旋圆锥体。公式参考：radius = maxRadius * (1 - t), angle = t * 50 * PI.
Mode 2: SCATTER (散落模式)
粒子分布在半径 8~20 的球体范围内。
重要： 在此模式下，粒子必须根据自身的随机速度向量进行自转 (Rotation)。
Mode 3: FOCUS (聚焦模式)
选中照片作为目标。
目标照片移动到相机前方 (0, 2, 35) 并放大 (Scale 4.5)。
其他粒子作为背景散开，并继续旋转，但照片固定。

5. MediaPipe 计算机视觉集成 (Computer Vision)
模型加载： 使用 FilesetResolver 和 HandLandmarker。设置 delegate: "GPU"。
手势识别算法 (Process Gestures)：
获取关键点：拇指(4), 食指(8), 手腕(0), 其他指尖(12,16,20)。
Pinch (捏合)： Math.hypot(thumb - index) < 0.05 -> 触发 FOCUS 模式。
Fist (握拳)： 四指尖到手腕平均距离 < 0.25 -> 触发 TREE 模式。
Open Hand (张开)： 四指尖到手腕平均距离 > 0.4 -> 触发 SCATTER 模式。
交互映射：
将手掌中心 (Landmark 9) 的标准化 X/Y 坐标，映射为 3D 场景根容器 (mainGroup) 的 rotation.y 和 rotation.x。

请输出完整的、包含所有这些细节的代码。





请根据以下内容对代码进行修改。
1.	删除“press h to hide controls”显示以及此功能。
2.	Make “merry Christmas” title thinner and 1 px smaller.
3.	UI 设计与 CSS 样式 
字体： 引入 Google Fonts 'Cinzel' (标题) 和 'Times New Roman' (正文)。
配色： 背景 #000000。主色调：香槟金，奶油白。

UI 组件：
Loader： 黑色全屏遮罩，中间为 40px 的金色旋转 Spinner (border-top: 1px solid #d4af37)，下方文字 "请稍等片刻"。初始化完成后淡出。
标题： 顶部居中 <h1> "Merry Christmas"，字号 54px，使用 CSS linear-gradient (白到金) 实现文字渐变色，并加辉光阴影。
Webcam： 在右下角创建一个容器 ，包含 <video> 和 160x120 的 <canvas> 用于 CV 推理。右下角的视频webcam容器，添加骨骼检测的展示（可以看见已检测出来的骨骼）。骨骼显示需要定位在容器中手的位置，并且不能超出容器。
说明框：。在左上角添加，有金色outline，黑色背景。内容如下：
手势交互说明
👊握拳 → 树形模式
✋ 张开手 → 散开模式
🤏 捏合 → 聚焦照片
4.	照片墙功能：
生成一张Canvas 纹理照片, 图片调用同目录里的图片文件，叫：photo.jpg。
照片必须包裹在金色的相框 (BoxGeometry) 中。
<img width="468" height="657" alt="image" src="https://github.com/user-attachments/assets/ce22e07a-f273-4b46-912b-183c4ef29274" />
