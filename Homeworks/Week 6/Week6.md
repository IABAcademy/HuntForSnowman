# 一、基础准备阶段
 
- **GPU消息缓冲清理 (ClearGPUMessageBuffer)**
  - 清空GPU通信缓冲区，确保新帧数据干净。
  - 使用了两次`ClearUnorderedAccessViewUint`操作。
 
- **距离场更新 (UpdateDistanceFieldAtlas)**
  - 计算动态距离场所需的Mip层级（Dispatch计算着色器）。
  - 生成流式加载请求并回读数据。
 
# 二、场景处理
 
- **GPU场景更新 (GPUSceneUpdate)**
  - 上传动态图元数据到GPU（如位置、材质参数等）。
  - 实例剔除（CullInstances）分阶段执行。
  - 虚拟高度场网格初始化（VirtualHeightfieldMesh）。
 
- **深度预渲染 (PrePass)**
  - 清空深度缓冲区（ClearDepthStencilView）。
  - 绘制网格世界坐标系（WorldGridMaterial）和雪地交互系统。
  - 运动矢量计算 (RenderVelocities)，清空速度缓冲区，绘制第一人称武器和雪球的运动矢量。
 
# 三、光照与阴影
 
- **HZB遮挡剔除 (HZB)**
  - 构建层级深度缓冲（Hierarchical Z-Buffer）。
  - 多级Mip生成（ReduceHZB分阶段Dispatch）。
 
- **阴影深度图生成 (ShadowDepths)**
  - 清空阴影图集（8192x2048分辨率）。
  - 分4次绘制场景阴影（WholeScene split0-3），包含大量网格实例：球体、立方体、斜坡等。
 
- **光照计算**
  - 天空大气LUT生成（Transmittance/MultiScattering等）。
  - 屏幕空间环境光遮蔽（SSAO）。
  - 延迟光照计算（DiffuseIndirectAndAO）。
  - 体积云渲染（VolumetricCloud）。
 
# 四、后期处理
 
- **时间超分辨率 (TemporalSuperResolution)**
  - 运动矢量扩展、历史拒绝、空间抗锯齿。
  - 最终输出到1910x908分辨率。
 
- **Bloom效果**
  - 多级高斯模糊（从30x15到955x454逐级处理）。
 
- **色调映射与合成**
  - 局部曝光（LocalExposure）。
  - Tonemapping颜色空间转换。
  - 编辑器轮廓高亮（EditorSelectionOutlines）。
 
# 五、编辑器相关
 
- **网格辅助线**
  - 世界坐标系网格（WorldGridMaterial）。
  - 虚拟高度场调试显示。
  - Gizmo绘制，使用GizmoMaterial绘制编辑器工具。
  - 半透明后期合成（CompositeEditorPrimitives）。
 
# 六、特殊效果
 
- **体积雾 (ExponentialHeightFog)**
  - 全屏雾效计算。
 
- **雪模拟粒子系统**
  - GPU粒子预处理（FXSystemPreRender）。
 
- **天空球渲染**
  - 使用M_SimpleSkyDome材质绘制天空盒。
 
# 性能关键点
 
- **阴影绘制**：分4次绘制2040x2040阴影图，包含大量实例化绘制（如12个球体实例）。
- **HZB构建**：通过多级Dispatch优化遮挡查询。
- **TSR抗锯齿**：消耗较多计算资源（186x89 Dispatch）。
- **体积云**：使用372x177分辨率计算云层。