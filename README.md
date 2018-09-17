# 前端复习整理
## 自己整理，想到什么是什么
### 动画
#### CSS3动画
- animation 与 keyframes结合
    - 6个属性 name、duration、timing-function（动画速度曲线linear、ease、cubic-bezier）、delay、iteration-count（播放次数）、direction(是否应该轮流反向播放动画,normal,alternate)
- transition 主要是起过渡效果
    - 4个属性 property（none|all|property）、duration、timing-function、delay
    - 当属性发生变化的时候，过渡效果才会起作用

#### js动画
- js强行实现
    - 不推荐，直接用js定时器修改style样式形成动画，会出现卡帧现象。
- canvas动画
    - canvas能够动起来也是js控制的，因此我认为属于js动画当中，主要操作就是绘制-清空-绘制，常用方法用Interval定时器。但是也可能出现卡帧的现象，建议动画渲染慢的时候使用，复杂的时候使用。
- requestAnimationFrame
    - requestAnimationFrame是另一种Web API，原理与setTimeout和setInterval类似，都是通过javascript持续循环的方法调用来触发动画动作。但是requestAnimationFrame是浏览器针对动画专门优化形成的APi，在性能上比另两者要好。<b>通常，我们将执行动画的每一步传到requestAnimationFrame中，在每次执行完后进行异步回调来连续触发动画效果。</b>
    - requestAnimationFrame的特点可以让他用在canvas动画上，也可以做到避免卡帧