---
outline: deep
---

# 大屏

## 认识大屏

开发网页时，我们适配最多的屏幕尺寸是：

- PC 端，1920 * 1080（也有少部分 4K 等）
- 移动端，750 * auto

### 那什么是大屏？

在生活中，我们经常看到一些比较大的屏幕，**比如指挥大厅、展厅、展会的大屏**。

### 大屏的应用场景

通常用在数据可视化，**比如：零售、物流、电力、环保、交通、医疗等领域。**

## 大屏适配方案

在学习大屏适配方案之前，我们先回顾一下移动端的适配方案有哪些？

1. 百分比设置（不推荐）
2. rem + 动态设置 html 的 font-size
3. vw 单位（推荐）
4. flex 弹性布局（推荐）

大屏的屏幕尺寸也是非常多的，那么大屏的适配方案有哪些？

1. 百分比设置
2. rem + 动态设置 html 的 font-size
3. vw 单位
4. flex 弹性布局
5. scale 等比例缩放（推荐）

### 方案一：rem + 动态设置 html 的 font-size

动态设置 html 字体大小 和 body 字体大小（lib_flexible.js）

- 将设计稿的宽（1920）平均分成 24 等份（移动端分成 10 等份），每一份为 80px
- html 字体大小设置为 80px，也就是 1rem，24rem = 1980px
- 需要使用 cssrem 插件

```js
function setRemUnit() {
  var rem = docEl.clientWidth / 24;
  docEl.style.fontSize = rem + 'px';
}
```

### 方案二：vw 单位

屏幕默认宽度为 100vw，那么 100vw = 1920px，1vw = 19.2px

- 需要使用 cssrem 插件， body 的宽高（1920px * 1080px）直接把 px 单位转换为 vw 单位

### 方案三：scale 等比例缩放（推荐）

直接使用 css3 的 scale 函数缩放网页

- 方案一： 直接根据宽度的比率进行缩放。（宽度比率 = 网页当前宽 / 设计稿宽）
- 方案二： 动态计算网页宽高比，决定是否按照宽度的比率进行缩放。

```js
// 设计稿：1920 * 1080
// 目标适配：1920 * 1080（1 * 1）、3840 * 2160（2 * 2）、7680 * 2160（4 * 2）

const designDraftWidth = 1920；
const designDraftHeight = 1080;
const designDraftRadio = designDraftWidth / designDraftHeight;

const targetWidth = document.documentElement.clientWidth || document.body.clientWidth;
const targetHeight = document.documentElement.clientHeight || document.body.clientHeight;
const targetRadio = targetWidth / targetHeight;

// 超宽屏
if(targetRadio > designDraftRadio) { 
  const scaleRadio = targetHeight / designDraftHeight; // 超宽屏，采用宽度进行缩放
  document.body.style = `width: ${designDraftWidth}px; height: ${designDraftHeight}px; transform: scale(${scaleRadio}); transform-origin: left top; position: relative; left: 50%; transformX: -50%;`
} else {
  const scaleRadio = targetWidth / designDraftWidth; // 默认参照宽度进行缩放
  document.body.style = `width: ${designDraftWidth}px; height: ${designDraftHeight}px; transform: scale(${scaleRadio}); transform-origin: left top;`
}

```

## 大屏开发注意事项

1. 字体大小设置问题（非 scale 方案考虑）
   - 如果使用 rem 或 vw 方案，在 js 添加样式的时候，单位需要手动设置 rem 或 vw
   - 第三方库的字体默认使用的都是 px，比如 element、echarts，因此需要层叠第三方库的样式
   - 当屏幕比例缩小放大的时候，有些字体还需要调整响应的字号
2. 图片模糊问题
   - 切图时，切1x、2x图，大屏用大图，小屏用小图
   - 建议都使用 svg矢量图，保证放大缩小不会失真
3. Echarts 渲染引擎的选择
   - 使用 svg 渲染引擎更好（svg 矢量图，放大不失真）
4. 动画卡顿优化
   - 创建新的渲染层，启用GPU加速，善用CSS3形变动画
   - 少用渐变和高斯模糊，当不需要动画的时候，及时关闭动画
