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
    强制方法
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

    //轮子
    function debouncing(fn, delay) {
        var timer = null;
        return function () {
            var context = this;
            var args = arguments;
            clearTimeout(timer)
            timer = setTimeout(function () {
                fn.apply(context,args)
            },delay)
        }
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
    
    //轮子
    function throttle(delay, fn) {
        var last = 0;
        return function () {
            var curr = +new Date()
            if(curr-last>delay){
            fn.apply(this,arguments)
            last = curr
            }
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

### 选择器
#### 选择器种类
1. 标签选择器(如：body,div,p,ul,li)

2. 类选择器(如：class="head",class="head_logo")

3. ID选择器(如：id="name",id="name_txt")

4. 全局选择器(如：*号)

5. 组合选择器(如：.head .head_logo,注意两选择器用空格键分开)

6. 后代选择器 (如：#head .nav ul li 从父集到子孙集的选择器)

7. 群组选择器 div,span,img {color:Red} 即具有相同样式的标签分组显示

8. 继承选择器(如：div p,注意两选择器用空格键分开)

9. 伪类选择器(如：就是链接样式,a元素的伪类，4种不同的状态：link、visited、active、hover。)

10. 字符串匹配的属性选择符(^ $ *三种，分别对应开始、结尾、包含)

11. 子选择器 (如：div>p ,带大于号>)

12. CSS 相邻兄弟选择器器 (如：h1+p,带加号+)

#### 优先级
- <b>!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性</b>
- 计算方法（数字之表达一个思想）
    - 内联样式表的权值为 1000
    - ID 选择器的权值为 100
    - Class 类选择器的权值为 10
    - HTML 标签选择器的权值为 1
#### 选择器及出现版本
<table class="dataintable">
<tbody><tr>
<th>选择器</th>
<th>例子</th>
<th>例子描述</th>
<th style="width:5%;">CSS</th>
</tr>

<tr>
<td><a title="CSS .class 选择器">.<i>class</i></a></td>
<td>.intro</td>
<td>选择 class="intro" 的所有元素。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS #id 选择器">#<i>id</i></a></td>
<td>#firstname</td>
<td>选择 id="firstname" 的所有元素。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS * 选择器">*</a></td>
<td>*</td>
<td>选择所有元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS element 选择器"><i>element</i></a></td>
<td>p</td>
<td>选择所有 &lt;p&gt; 元素。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS element,element 选择器"><i>element</i>,<i>element</i></a></td>
<td>div,p</td>
<td>选择所有 &lt;div&gt; 元素和所有 &lt;p&gt; 元素。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS element element 选择器"><i>element</i> <i>element</i></a></td>
<td>div p</td>
<td>选择 &lt;div&gt; 元素内部的所有 &lt;p&gt; 元素。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS element>element 选择器"><i>element</i>&gt;<i>element</i></a></td>
<td>div&gt;p</td>
<td>选择父元素为 &lt;div&gt; 元素的所有 &lt;p&gt; 元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS element+element 选择器"><i>element</i>+<i>element</i></a></td>
<td>div+p</td>
<td>选择紧接在 &lt;div&gt; 元素之后的所有 &lt;p&gt; 元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS [attribute] 选择器">[<i>attribute</i>]</a></td>
<td>[target]</td>
<td>选择带有 target 属性所有元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS [attribute=value] 选择器">[<i>attribute</i>=<i>value</i>]</a></td>
<td>[target=_blank]</td>
<td>选择 target="_blank" 的所有元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS [attribute~=value] 选择器">[<i>attribute</i>~=<i>value</i>]</a></td>
<td>[title~=flower]</td>
<td>选择 title 属性包含单词 "flower" 的所有元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS [attribute|=value] 选择器">[<i>attribute</i>|=<i>value</i>]</a></td>
<td>[lang|=en]</td>
<td>选择 lang 属性值以 "en" 开头的所有元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS :link 选择器">:link</a></td>
<td>a:link</td>
<td>选择所有未被访问的链接。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS :visited 选择器">:visited</a></td>
<td>a:visited</td>
<td>选择所有已被访问的链接。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS :active 选择器">:active</a></td>
<td>a:active</td>
<td>选择活动链接。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS :hover 选择器">:hover</a></td>
<td>a:hover</td>
<td>选择鼠标指针位于其上的链接。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS :focus 选择器">:focus</a></td>
<td>input:focus</td>
<td>选择获得焦点的 input 元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS :first-letter 选择器">:first-letter</a></td>
<td>p:first-letter</td>
<td>选择每个 &lt;p&gt; 元素的首字母。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS :first-line 选择器">:first-line</a></td>
<td>p:first-line</td>
<td>选择每个 &lt;p&gt; 元素的首行。</td>
<td>1</td>
</tr>

<tr>
<td><a title="CSS :first-child 选择器">:first-child</a></td>
<td>p:first-child</td>
<td>选择属于父元素的第一个子元素的每个 &lt;p&gt; 元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS :before 选择器">:before</a></td>
<td>p:before</td>
<td>在每个 &lt;p&gt; 元素的内容之前插入内容。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS :after 选择器">:after</a></td>
<td>p:after</td>
<td>在每个 &lt;p&gt; 元素的内容之后插入内容。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS :lang(language) 选择器">:lang(<i>language</i>)</a></td>
<td>p:lang(it)</td>
<td>选择带有以 "it" 开头的 lang 属性值的每个 &lt;p&gt; 元素。</td>
<td>2</td>
</tr>

<tr>
<td><a title="CSS element1~element2 选择器"><i>element1</i>~<i>element2</i></a></td>
<td>p~ul</td>
<td>选择前面有 &lt;p&gt; 元素的每个 &lt;ul&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS [attribute^=value] 选择器">[<i>attribute</i>^=<i>value</i>]</a></td>
<td>a[src^="https"]</td>
<td>选择其 src 属性值以 "https" 开头的每个 &lt;a&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS [attribute$=value] 选择器">[<i>attribute</i>$=<i>value</i>]</a></td>
<td>a[src$=".pdf"]</td>
<td>选择其 src 属性以 ".pdf" 结尾的所有 &lt;a&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS [attribute*=value] 选择器">[<i>attribute</i>*=<i>value</i>]</a></td>
<td>a[src*="abc"]</td>
<td>选择其 src 属性中包含 "abc" 子串的每个 &lt;a&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :first-of-type 选择器">:first-of-type</a></td>
<td>p:first-of-type</td>
<td>选择属于其父元素的首个 &lt;p&gt; 元素的每个 &lt;p&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :last-of-type 选择器">:last-of-type</a></td>
<td>p:last-of-type</td>
<td>选择属于其父元素的最后 &lt;p&gt; 元素的每个 &lt;p&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :only-of-type 选择器">:only-of-type</a></td>
<td>p:only-of-type</td>
<td>选择属于其父元素唯一的 &lt;p&gt; 元素的每个 &lt;p&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :only-child 选择器">:only-child</a></td>
<td>p:only-child</td>
<td>选择属于其父元素的唯一子元素的每个 &lt;p&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :nth-child(n) 选择器">:nth-child(<i>n</i>)</a></td>
<td>p:nth-child(2)</td>
<td>选择属于其父元素的第二个子元素的每个 &lt;p&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :nth-last-child(n) 选择器">:nth-last-child(<i>n</i>)</a></td>
<td>p:nth-last-child(2)</td>
<td>同上，从最后一个子元素开始计数。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :nth-of-type(n) 选择器">:nth-of-type(<i>n</i>)</a></td>
<td>p:nth-of-type(2)</td>
<td>选择属于其父元素第二个 &lt;p&gt; 元素的每个 &lt;p&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :nth-last-of-type(n) 选择器">:nth-last-of-type(<i>n</i>)</a></td>
<td>p:nth-last-of-type(2)</td>
<td>同上，但是从最后一个子元素开始计数。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :last-child 选择器">:last-child</a></td>
<td>p:last-child</td>
<td>选择属于其父元素最后一个子元素每个 &lt;p&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :root 选择器">:root</a></td>
<td>:root</td>
<td>选择文档的根元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :empty 选择器">:empty</a></td>
<td>p:empty</td>
<td>选择没有子元素的每个 &lt;p&gt; 元素（包括文本节点）。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :target 选择器">:target</a></td>
<td>#news:target</td>
<td>选择当前活动的 #news 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :enabled 选择器">:enabled</a></td>
<td>input:enabled</td>
<td>选择每个启用的 &lt;input&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :disabled 选择器">:disabled</a></td>
<td>input:disabled</td>
<td>选择每个禁用的 &lt;input&gt; 元素</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :checked 选择器">:checked</a></td>
<td>input:checked</td>
<td>选择每个被选中的 &lt;input&gt; 元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS :not(selector) 选择器">:not(<i>selector</i>)</a></td>
<td>:not(p)</td>
<td>选择非 &lt;p&gt; 元素的每个元素。</td>
<td>3</td>
</tr>

<tr>
<td><a title="CSS ::selection 选择器">::selection</a></td>
<td>::selection</td>
<td>选择被用户选取的元素部分。</td>
<td>3</td>
</tr>
</tbody></table>

### px rem em
#### px
px像素（Pixel）。相对长度单位。像素px是相对于显示器屏幕分辨率而言的。
#### em
em是相对长度单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。
- em的值并不是固定的
- em会继承父级元素的字体大小
#### rem
rem是CSS3新增的一个相对单位（root em，根em）
Tip dpr => scale
```
if (!dpr && !scale) {
    var isAndroid = win.navigator.appVersion.match(/android/gi);
    var isIPhone = win.navigator.appVersion.match(/iphone/gi);
    var devicePixelRatio = win.devicePixelRatio;
    if (isIPhone) {
        // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
        if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {                
            dpr = 3;
        } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)){
            dpr = 2;
        } else {
            dpr = 1;
        }
    } else {
        // 其他设备下，仍旧使用1倍的方案
        dpr = 1;
    }
    scale = 1 / dpr;
}
```


### Array String API
#### Array API
- 修改原数组的方法
    - pop() 删除最后一个元素
    - push() 在末尾插入元素
    - shift() 删除第一个元素
    - unshift() 在头部插入元素
    - reverse() 反转数组
    - splice() 删除数组
    - sort() 排序
- 不修改原数组的方法
    - concat() 连接多个数组，返回一个副本（新数组）
    - join() 连接数组内如，返回一个字符串
    - slice() 从某个已有的数组返回选定的元素
    - toSource() 返回该对象的源代码
    - toLocaleString() 把数组转换为本地数组，并返回结果
    - valueOf() 返回数组对象的原始值

#### String API
- API所有方法都不会修改原字符串

### js基本数据类型与引用类型
#### 基本数据类型
- number，string，undefined，null，boolean，symbol
#### 引用类型
- object，Array，Function，Dat，RegExp
#### typeof有几种结果
- 6种 number，string，undefined，object，function

### 箭头函数和普通函数的区别
- 箭头函数不能作为构造函数，不能使用new
- 箭头函数没有自己的this，他的this是捕获上下文的this
- 箭头函数没有arguments,但是可以使用rest参数
- 箭头函数通过 call()  或   apply() 方法调用一个函数时，只传入了一个参数，对 this 并没有影响(修改不了this)
- 箭头函数没有原型属性

### 提到原型属性，引出原型链
- 每个函数都有自己的显示原型属性 prototype
- 每个引用类型（函数，数组，对象）都拥有__proto__属性，这个称之为隐式原型。
- 每个引用类型的隐式原型都指向它的构造函数的显示原型,隐式原型的constructorj就是他的构造函数
- 显示原型又有隐式原型，以此类推一直到null

### 提到原型链，那就说一下继承
```
function Animal(name){
    this.name = name;
}
Animal.eat =fucntion(food){
    console.log(food);
}
```
#### 原型继承
```
function Cat(){
}
Cat.prototype = new Animal();
Cat.protyotype.name = 'cat';
//　Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.eat('fish'));
```

#### 构造继承
```
function Cat(name='Tom'){
    Animal.call(this);
    this.name = name;
}
// Test Code
var cat = new Cat();
console.log(cat.name);
```

### 组合继承
```
function Cat(name='Tom'){
    Animal.call(this);
    this.name = name;
}
Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
// Test Code
var cat = new Cat();
console.log(cat.name);

//优化
function Cat(name='Tom'){
    Animal.call(this);
    this.name = name;
}
Cat.prototype = Animal.prototype;
Cat.prototype.constructor = Cat;

```

### ES6观察者模式,事件绑定，解绑，触发
```
class eventObs {
    constructor(){
        this.handleFunc={}
    }

    add(type,func){
        if(this.handleFunc[type]){
            if(this.handleFunc[type].indexOf(func)===-1){
                this.handleFunc[type].push(func);
            }
        }else{
            this.handleFunc[type]=[func];
        }

    };

    fire(type,func){
        try{

            if(arguments.length===1) {
                let target = this.handleFunc[type];
                let count = target.length;
                for (var i = 0; i < count; i++) {
                    target[i]();
                }
            }else{
                let target = this.handleFunc[type];
                let index=target.indexOf(func);
                if(index===-1)throw error;
                func();
            }
            return true;
        }catch (e){
            console.error('error');
            return false;
        }
    };

    remove(type,func){
        try{
            let target = this.handleFunc[type];
            let index=target.indexOf(func);
            if(index===-1)throw error;
            target.splice(index,1);
        }catch (e){
            console.error('error');
        }

    };

    once(type,func) {

        this.fire(type, func)?
            this.remove(type, func):null;

    }
}
引用自：https://www.jianshu.com/p/10a20df72bf2
```

### Http
#### 报文结构
- 请求报文
    - 请求行（请求方式，URL，http版本）
    - 请求头（各种属性，cookie，accept，if-modified-since）
    - 换行
    - 请求数据
- 响应报文
    - 响应行（http版本，状态码+描述）
    - 响应头
    - 响应体

#### 常见的请求头和响应头
- 请求头
    - Accept： 浏览器可接受的MIME类型。
    - Authorization：授权信息，通常出现在对服务器发送的WWW-Authenticate头的应答中。
    - If-Modified-Since：客户机通过这个头告诉服务器，资源的缓存时间。只有当所请求的内容在指定的时间后又经过修改才返回它，否则返回304“Not Modified”应答。
    - Referer：客户机通过这个头告诉服务器，它是从哪个资源来访问服务器的(防盗链)。
    - User-Agent：User-Agent头域的内容包含发出请求的用户信息。
    - Cookie：客户机通过这个头可以向服务器带数据，这是最重要的请求头信息之一。
    - Host
    - Connection:Keep-Alive
    - Cache-Control
    - Content-Type (application/x-www-form-urlencoded/text/xml)
- 响应头
    - Allow：服务器支持哪些请求方法(如GET、POST等)
    - Allow：服务器支持哪些请求方法(如GET、POST等)
    - Access-Control-Allow-Origin
    - Cache-Control
    - ETag
    - Expires
    - Last-Modified
    - Server
    - Set-Cookie

#### 缓存
- 强缓存
    - expires (http 1.0)<br/>
    标准格林威治(GMT)时间，如果发送请求的时间在expires之前，那么本地缓存始终有效，否则就会发送请求到服务器来获取资源。<br>
    expires 的一个缺点就是，返回的到期时间是服务器端的时间，这样存在一个问题，如果客户端的时间与服务器的时间相差很大，那么误差就很大。
    - Cache-Control （http1.1）<br>
    主要是利用该字段的max-age值来进行判断，它是一个相对值；资源第一次的请求时间和Cache-Control设定的有效期，计算出一个资源过期时间，再拿这个过期时间跟当前的请求时间比较，如果请求时间在过期时间之前，就能命中缓存，否则就不行
        - no-cache：不使用本地缓存。需要使用缓存协商，先与服务器确认返回的响应是否被更改，如果之前的响应中存在ETag，那么请求的时候会与服务端验证，如果资源未被更改，则可以避免重新下载。
        - no-store：直接禁止游览器缓存数据，每次用户请求该资源，都会向服务器发送一个请求，每次都会下载完整的资源。
        - public：可以被所有的用户缓存，包括终端用户和CDN等中间代理服务器。
        - private：只能被终端用户的浏览器缓存，不允许CDN等中继缓存服务器对其缓存。
    - <b>Tip:Cache-Control > expires</b>
    - 命中返回200（from cache）
- 协商缓存
    - Last-Modified/If-Modified-Since<br>
     二者的值都是GMT格式的时间字符串
    - Etag/If-None-Match<br>
    这两个值是由服务器生成的每个资源的唯一标识字符串，只要资源有变化就这个值就会改变
    - 命中返回304（not modified）

#### http1.0 http1.1 http2.0
- 1.0和1.1
    - 1.0需要keep-alive参数来告知服务器端要建立一个长连接，而HTTP1.1默认支持长连接
    - 1.1中新增了24个错误状态响应码
    - 1.0没有Host
    - 1.1支持只发送header信息(不带任何body信息),节约带宽
    - 1.1增加了缓存处理方式 ETag
- 1.1和2.0
    - 1.1不支持数据压缩，2.0支持
    - 2.0使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比HTTP1.1大了好几个数量级。
    - 2.0的web server请求数据的时候，服务器会顺便把一些客户端需要的资源一起推送到客户端，免得客户端再次创建连接发送请求到服务器端获取。

#### http与https
- HTTPS协议需要到CA申请证书，一般免费证书很少，需要交费。
- HTTP协议运行在TCP之上，所有传输的内容都是明文，HTTPS运行在SSL/TLS之上，SSL/TLS运行在TCP之上，所有传输的内容都经过加密的。
- HTTP和HTTPS使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
- HTTPS可以有效的防止运营商劫持，解决了防劫持的一个大问题。

- Tip CA证书获取过程
    - 服务器生成公私钥发给CA机构
    - CA机构生成公私钥，然后用私钥对后端的公钥加密并生成CA证书
    - CA机构将生成的CA证书返回给服务器
    - 浏览器内置CA证书和CA的公钥

#### HTTP的请求方法OPTIONS
- OPTIONS <br>
OPTIONS方法是用于请求获得由Request-URI标识的资源在请求/响应的通信过程中可以使用的功能选项。通过这个方法，客户端可以在采取具体资源请求之前，决定对该资源采取何种必要措施，或者了解服务器的性能。<br>
这个方法很有趣，但极少使用。它用于获取当前URL所支持的方法。若请求成功，则它会在HTTP头中包含一个名为“Allow”的头，值是所支持的方法，如“GET, POST”。
- GET
- HEAD
- POST
- PUT
- DELETE
- TRACE<br>
TRACE方法是用来调用一个远程的请求信息应用程序层的循环后退。最后的请求容器应该像一个200回复实体主体那样反映顾客接受返回的信息。最后的容器或者是原服务器或者是第一代理器或接收在请求中一个Max-Forwards 零数位的网关
- CONNECT<br>
CONNECT这个方法的作用就是把服务器作为跳 板，让服务器代替用户去访问其它网页，之后把数据原原本本的返回给用户。