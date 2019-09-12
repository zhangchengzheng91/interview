[返回目录](../README.md)
##### CSS 相关面试问题
1. CSS 盒模型
    ---
    |  标准盒模型 | 怪异盒模型（IE 盒模型）|
    |---|---|
    |div { box-sizing: content-box; }|div { box-sizing: border-box; }|
    |![](./assets/content-box.png)|![](./assets/border-box.png)|
    |<code>width = content</code>|<code>width = border + padding + content</code>|
2. css 新属性：flex 布局
    ---
    参考链接：[阮一峰的网络日志 >> Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
    ![](./assets/flex.png)

    | 容器属性||
    |:---|:---|
    |flex-direction:主轴的方向|row \| row-reverse \| column \| column-reverse||
    |flex-wrap:换行|nowrap \| wrap \| wrap-reverse|
    |flex-flow|\<flex-direction\> \|\| \<flex-wrap\>简写形式
    |justify-content:主轴上的对齐方式|flex-start \| flex-end \| center \| space-between \| space-around
    |align-items:交叉轴上的对齐方式|flex-start \| flex-end \| center \| baseline \| stretch
    |align-content:多根轴线的对齐方式|flex-start \| flex-end \| center \| space-between \| space-around \| stretch

    |项目属性||
    |:---|:---|
    |order:项目的排列顺序|数值越小，排列越靠前，默认为0|
    |flex-grow:项目的放大比例|默认为0，即如果存在剩余空间，也不放大|
    |flex-shrink:项目的缩小比例|默认为1，即如果空间不足，该项目将缩小|
    |flex-basis:在分配多余空间之前，项目占据的主轴空间|\<length> \| auto|
    |flex|\<flex-grow> \|\| \<flex-shrink> \|\| \<flex-basis>简写形式，快捷值：auto \| none
    |align-self:允许单个项目有与其他项目不一样的对齐方式|auto \| flex-start \| flex-end \| center \| baseline \| stretch
3. less、sass 相关的知识点：嵌套、变量、模块化
参考链接：[官方文档](https://www.html.cn/doc/sass/#features)
    Tips: 注意@include 与 @extend 的区别
    1.  变量， **them.scss，定义全局基本样式变量**
        ```scss
            $width: 5em;
            #main {
              width: $width;
            }
        ```
    1.  @mixin，结合默认参数(关键字参数)，可变参数
        ```scss
            @mixin sexy-border($color, $width: 1px) {
                border: {
                color: $color;
                    width: $width;
                style: dashed;
                }
            }
            p {
                @include sexy-border(blue);
            }
            h1 {
                @include sexy-border(blue, 2px)
            }
        ```
        编译结果
        ```css
            p {
                border-color: blue;
                border-width: 1px;
                border-style: dashed;
            }
            h1 {
                border-color: blue;
                border-width: 2in;
                border-style: dashed;
            }
        ```
    1.  嵌套
        ```scss
            #main p {
                color: #00ff00;
                width: 97%;
                .redbox {
                    background-color: #ff0000;
                    color: #000000;
                }
            }
        ```
    1.  引用父选择器: &
        ```scss
            a {
                font-weight: bold;
                text-decoration: none;
                &:hover {
                    text-decoration: underline;
                }
                body.firefox & {
                    font-weight: normal;
                }
            }
        ```
    1.  @extend
         ```scss
             .error {
                 border: 1px #f00;
                 background-color: #fdd;
             }
             .seriousError {
                 @extend .error;
                 border-width: 3px;
             }
        ```
        编译结果：
        ```css
            .error, .seriousError {
                border: 1px #f00;
                background-color: #fdd;
            }
            .seriousError {
                border-width: 3px;
            }
        ```
    1.  @import，模块化
        ```scss
            @import "foo.css";
            @import "foo" screen;
            @import "http://foo.com/bar";
            @import url(foo);
        ```
    1.  @media
        ```scss
            .sidebar {
                width: 300px;
                @media screen and (orientation: landscape) {
                  width: 500px;
                }
            }
        ```
4. 媒体查询
5. css 选择器的权重
    !important > 行内样式（1000） > ID 选择器（#， 100） > 类选择器 | 属性选择器 | 伪元素选择器（. | \[attribute] |, 10） > 元素选择器（div, 1）
    属性选择器：[参考链接](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors)
    1.  \[attr]:表示带有以 attr 命名的属性的元素。例：存在title属性的<a> 元素, 如下选择
    1.  \[attr=value]:表示带有以 attr 命名的属性的元素，并且该属性是一个以空格作为分隔的值列表，其中**至少**一个值匹配"value"。
    1.  \[attr~=value]:表示带有以 attr 命名的属性的元素，并且该属性是一个以空格作为分隔的值列表，其中**至少**一个值匹配"value"。
    1.  \[attr|=value]:表示带有以 attr 命名的属性的元素，属性值为“value”或是以“value-”为前缀（"-"为连字符，Unicode编码为U+002D）开头。典型的应用场景是用来来匹配语言简写代码（如zh-CN，zh-TW可以用zh作为value）。
    1.  \[attr^=value]:表示带有以 attr 命名的属性，且属性值是以"value"开头的元素。
    1.  \[attr$=value]:表示带有以 attr 命名的属性，且属性值是以"value"结尾的元素。
    1.  \[attr*=value]:表示带有以 attr 命名的属性，且属性值包含有"value"的元素。
        ```css
        div[lang] {
          font-weight: bold;
        }
        div[lang="pt"] {
          color: green;
        }
        div[lang~="en-us"] {
          color: blue;
        }
        div[lang|="zh"] {
          color: red;
        }
        div[data-lang="zh-TW"] {
          color: purple;
        }
        ```
        <div lang="en-us en-gb en-au en-nz">Hello World!</div>
        <div lang="pt">Olá Mundo!</div>
        <div lang="zh-CN">世界您好！</div>
        <div lang="zh-TW">世界您好！</div>
        <div data-lang="zh-TW">?世界您好！</div>
    伪元素选择器：[参考链接](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Introduction_to_CSS/Pseudo-classes_and_pseudo-elements)
    ```css
    div:after {}
    div:before {}
    ```
6. 父元素不确定宽高，该元素怎么上下左右居中显示
7. BFC IFC
8. css-modules相关知识点  
参考链接：[What are CSS Modules and why do we need them?](https://css-tricks.com/css-modules-part-1-need/)  
CSS 模块化的解决方案：  
    -- Inline Style：这种方案彻底抛弃CSS，使用Javascript 或 JSON 来写样式，能给 CSS 提供Javascript 同样强大的模块化能力。
    ```javascript
    var divStyle = {
      color: 'white',
      backgroundImage: 'url(' + imgUrl + ')',
      WebkitTransition: 'all', // note the capital 'W' here
      msTransition: 'all' // 'ms' is the only lowercase vendor prefix
    };

    ReactDOM.render(<div style={divStyle}>Hello World!</div>, mountNode);
    ```
    -- CSS Modules
Part1: What are CSS Modules and why do we need them? (You are here!)
    CSS 模块化当中遇到的问题：依旧使用 CSS，但使用 Javascript 来管理样式依赖。webpack css-loader 内置 CSS Modules 功能。
    1.  全局污染
    1.  命名混乱
    1.  依赖管理不彻底：组件应该相互独立，引入一个组件时，应该只引入它所需要的CSS样式（参考antd 按需加载：babel-plugin-import ）。
    1.  无法变量共享：复杂组件要使用 Javascript 和 CSS 来共同处理样式，就会造成有些变量在 Javascript 和 CSS 中亢余，而预编译语言不能提供给跨 Javascript 和 CSS 共享变量的这种能力。
    1.  代码压缩不彻底：由于移动端网络的不确定性，现代项目对 CSS 压缩的要求已经达到了变态的程度。很多压缩工具为了节省一个字节，会把16px 转化成 1pc，但是对非常长的类名确无能为力。
    开启 CSS Modules：
    ```javascript
    module.exports = {
        module: {
            rules: [
                {
                    test: /\.css$/i,
                    loader: 'css-loader',
                    options: {
                    modules: {
                            mode: 'local',
                            localIdentName: '[path][name]__[local]--[hash:base64:5]',
                            context: path.resolve(__dirname, 'src'),
                            hashPrefix: 'my-custom-hash',
                        },
                    },
                },
            ],
        },
    };
    ```
    CSS Module 实现的内容：
    1.  所有样式都局部化，解决了命名冲突和全局污染问题
    1.  class 名的生成规则配置灵活，可以以此来压缩 class 名
    1.  只需要引用 Javascript，就能搞定组件所有的 Javascript 和 CSS
    1.  依然是CSS，学习成本基本为0  
class 命名技巧 BEM [BEM 101](https://css-tricks.com/bem-101/)：
-- Block：对应模块名，如 Dialog。
-- Element：对应模块中的节点名，如 Confirm Button。
-- Modifier：对应节点的状态，如 disabled 和 highlight.  
CSS files in which all <code>class names</code> and <code>animation names</code> are scoped locally by default.  
[the classes] are dynamically generated, unique, and mapped to the correct styles.
Part2: Getting Started with CSS Modules
Prat3: React + CSS Modules = 😍

