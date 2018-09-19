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