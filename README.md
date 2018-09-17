# 前端复习整理
## 自己整理，想到什么是什么
### 动画
#### CSS3动画
- animation 与 keyframes结合
    - 6个属性 name、duration、timing-function（动画速度曲线linear、ease、cubic-bezier）、delay、iteration-count（播放次数）、direction(是否应该轮流反向播放动画,normal,alternate)
    - 简单实例<br/>
    `animation:mymove 5s infinite;`
- transition 主要是起过渡效果
    - 4个属性 property（none|all|property）、duration、timing-function、delay
    - 当属性发生变化的时候，过渡效果才会起作用
    - 简单实例<br/>
    `transition: all 2s;`

#### js动画
- js强行实现
    - 不推荐，直接用js定时器修改style样式形成动画，会出现卡帧现象。
- canvas动画
    - canvas能够动起来也是js控制的，因此我认为属于js动画当中，主要操作就是绘制-清空-绘制，常用方法用Interval定时器。但是也可能出现卡帧的现象，建议动画渲染慢的时候使用，复杂的时候使用。
- requestAnimationFrame
    - requestAnimationFrame是另一种Web API，原理与setTimeout和setInterval类似，都是通过javascript持续循环的方法调用来触发动画动作。但是requestAnimationFrame是浏览器针对动画专门优化形成的APi，在性能上比另两者要好。<b style="color:red">通常，我们将执行动画的每一步传到requestAnimationFrame中，在每次执行完后进行异步回调来连续触发动画效果。</b>
    - requestAnimationFrame的特点可以让他用在canvas动画上，也可以做到避免卡帧
    - 简单实例：<br/>
        ```
        var start = null;
        var element = document.getElementById('SomeElementYouWantToAnimate');
        element.style.position = 'absolute';
        function step(timestamp) {
            if (!start) start = timestamp;
            var progress = timestamp - start;
            element.style.left = Math.min(progress / 10, 200) + 'px';
            if (progress < 2000) {
                window.requestAnimationFrame(step);
                }
        }
        window.requestAnimationFrame(step);
        ```
- Tip 16ms <br/>
我们设置的setInterval时间间隔是16ms。一般认为人眼能辨识的流畅动画为每秒60帧，这里16ms比(1000ms/60)帧略小一些，但是一般可仍为该动画是流畅的。<br/> 
在很多移动端动画性能优化时，一般使用16ms来进行节流处理连续触发的浏览器事件。例如对touchmove、scroll事件进行节流等。通过这种方式减少持续事件的触发频率，可以大大提升动画的流畅性。

### 懒加载
#### 图片懒加载
- 具体实现：给image设定attr-img属性，值设置为图片的url，当滚轮滚动到图片位置，js操作将attr-img设置设置到image的url。
- 为了节约 window.onscroll的次数 ,提高性能, 设计了函数节流和函数防抖两种模式

#### 防抖函数
- 多次触发事件后，事件处理函数只执行一次，并且是在触发操作结束时执行。
- 原理：对处理函数进行延时操作，若设定的延时到来之前，再次触发事件，则清除上一次的延时操作定时器，重新定时。
-  简单实例：<br/>
    ```
    let timer;
    window.onscroll  = function () {
        if(timer){
            clearTimeout(timer)
        }
        timer = setTimeout(function () {
            //滚动条位置
            let scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
            console.log('滚动条位置：' + scrollTop);
            timer = undefined;
        },200)
    }
    ```

#### 节流函数
- 触发函数事件后，短时间间隔内无法连续调用，只有上一次函数执行后，过了规定的时间间隔，才能进行下一次的函数调用。
- 原理：对处理函数进行延时操作，若设定的延时到来之前，再次触发事件，则清除上一次的延时操作定时器，重新定时。
- 简单实例：<br/>
    ```
    let startTime = Date.now();
    //开始时间
    let time = 500; //间隔时间
    let timer;
    window.onscroll = function throttle(){
        let currentTime = Date.now();
        if(currentTime - startTime >= time){
            let scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
            console.log('滚动条位置：' + scrollTop);
            startTime = currentTime;
        }else{
            clearTimeout(timer);
            timer = setTimeout(function () {
                throttle()
            }, 50);
        }
    }
    ```

### flex 布局（常问）
#### 容器的属性
- flex-direction 
    - 定主轴的方向，项目的排列方向
    ```
    .box {
        flex-direction: row | row-reverse | column | column-reverse;
    }
    ```
    - row（默认值）：主轴为水平方向，起点在左端。
    - row-reverse：主轴为水平方向，起点在右端。
    - column：主轴为垂直方向，起点在上沿。
    - column-reverse：主轴为垂直方向，起点在下沿。
- flex-wrap 
    - 是否换行
    ```
    .box{
        flex-wrap: nowrap | wrap | wrap-reverse;
    }
    ```
    - nowrap（默认）：不换行。
    - wrap：换行，第一行在上方。
    - wrap-reverse：换行，第一行在下方。
- flex-flow 
    - flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
    ```
    .box {
        flex-flow: <flex-direction> || <flex-wrap>;
    }
    ```
- justify-content 
    - 项目在<b style="color:red">主轴</b>上的对齐方式
    ```
    .box {
        justify-content: flex-start | flex-end | center | space-between | space-around;
    }
    ```
    - flex-start（默认值）：左对齐
    - flex-end：右对齐
    - center： 居中
    - space-between：两端对齐，项目之间的间隔都相等。
    - space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍
- align-items 
    - 定义项目在<b style="color:red">交叉轴</b>上如何对齐方式
    ```
    .box {
        align-items: flex-start | flex-end | center | baseline | stretch;
    }
    ```
    - flex-start：交叉轴的起点对齐。
    - flex-end：交叉轴的终点对齐。
    - center：交叉轴的中点对齐。
    - baseline: 项目的第一行文字的基线对齐。
    - stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
- align-content 
    - 定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
    ```
    .box {
        align-content: flex-start | flex-end | center | space-between | space-around | stretch;
    }
    ```
    - flex-start：与交叉轴的起点对齐。
    - flex-end：与交叉轴的终点对齐。
    - center：与交叉轴的中点对齐。
    - space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
    - space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
    - stretch（默认值）：轴线占满整个交叉轴。
- Tip flex布局有几根轴？
    - 水平的主轴（main axis）
    - 竖直的交叉轴

#### 项目的属性
- order 
    - 定义项目的排列顺序。数值越小，排列越靠前，默认为0
    ```
    .item {
        order: <integer>;
    }
    ```
- flex-grow 
    - 定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大
    ```
    .item {
        flex-grow: <number>; /* default 0 */
    }
    ```
    - 如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
- flex-shrink 定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小
    ```
    .item {
    flex-shrink: <number>; /* default 1 */
    }
    ```
    - 如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。

    - 负值对该属性无效。
- flex-basis 
    - 属性定义了在分配多余空间之前，项目占据的主轴空间（main size）.浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
    ```
    .item {
        flex-basis: <length> | auto; /* default auto */
    }
    ```
    - 它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。
- flex
    - flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。
    ```
    .item {
        flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
    }
    ```
    - auto 等于 (1 1 auto) 
    - none 等于 (0 0 auto)
    - 1 等于 (1 1 0%)
- align-self
    - 允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。
    ```
    .item {
        align-self: auto | flex-start | flex-end | center | baseline | stretch;
    }
    ```
    - 该属性可能取6个值，除了auto，其他都与align-items属性完全一致。