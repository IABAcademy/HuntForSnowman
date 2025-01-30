# 基于Unreal Open Day 2022教程实现交互雪的思考与步骤

基于[Unreal Open Day 2022教程](https://www.bilibili.com/video/BV1gW4y1x7Sz/?spm_id_from=333.788.comment.all.click)实现了一个简单的交互雪地，能根据玩家运动轨迹写入RenderTarget，并用虚拟纹理与虚拟高度场网格体与RenderTarget关联，实现实时更改雪地高度。
## 二、实现步骤

### 1. 创建Render Target

### 2. 创建材质和材质实例

### 3. 设定画板蓝图

### 4. 编写绘制逻辑
在蓝图中，创建一个Actor作为“画板”。
该“画板”将玩家的世界坐标位置转换为UV坐标，并使用Draw Material to Render Target节点对RenderTarget进行绘制。

### 5.优化雪地效果
- **跟随玩家移动**：为了节省性能，我们只关心玩家特定范围内的交互痕迹。因此，需要确保画板始终跟随玩家移动。
- **更新图案**：每当玩家移动时，我们需要更新雪地上的图案。这可以通过创建一个副本Render Target来实现，将原始的Render Target绘制到副本上，然后对副本进行平移和采样，最后将更新后的图案绘制回原始的Render Target。

### 6. 虚拟纹理

- **虚拟纹理**：设置虚拟纹理，启用虚拟纹理支持
- **虚拟高度场网格体**：为了实现更真实的雪地交互效果，可以将RenderTarget的颜色设置其对应材质的世界偏移量，并将该材质与虚拟高度场网格体，以模拟雪地的起伏和变形。