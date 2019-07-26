# CSS33D实现折叠收据

> 参考原文来自周刊中2019年7月19日鹏程哥分享的文章
> https://juejin.im/post/5d26b1fde51d4577583ddd54

这篇文章和上一篇文章[折叠照片](http://172.17.10.41:8020/wanzizhe/book/CSS33D/fold-picture.html)样式上有着相似的地方，但是实现方式还是很不一样的，现在让我们看下效果展示：

![](/CSS33D/img/fold-receipt/fold-receipt.gif)

看起来是不是很高级，其实只不过就是CSS33D的属性和伪类元素的完美运用就可以做到，其中的知识点我们都是很熟悉的，让我们来看下代码~

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=
    , initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>

        html {   box-sizing: border-box; } *, *:before, *:after {   box-sizing: inherit; }

        body {
            margin: 40px;
            background: #ccc;
        }

        .receipt {
            width: 400px;
            max-width: 100%;
            margin: auto;
            position: relative;
        }
        /* 上部分和下部分公用的样式 */
        .receipt__half {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            width: 100%;
            position: relative;
            background: white;
            perspective: 1000px;

        }
        /* 上部分和下部分公用的伪类样式 */
        .receipt__half::after {
            content: '';
            display: block;
            width: 100%;
            height: 100px;
            position: absolute;
            left: 0;
            backface-visibility: hidden;
            transition: all .8s ease;
            transition-delay: .35s;
        }
        /* 展开后上部分和下部分公用的伪类样式 */
        .active .receipt__half::after {
            transition-delay: 0s;
        }
        /* 上部分样式 */
        .upper {
            padding: 20px;
            z-index: 10;
        }
        /* 上部分伪类样式 */
        .upper::after {
            top: 100%;
            background: darken(white, 3%);
            border-top: 1px dashed #dedede;
            transform: rotate3d(1, 0, 0, -90deg);/*这里需要大家想象一下，在收据收起的时候，上部分伪类是沿着上边框绕X轴逆时针旋转90°的*/
            transform-origin: top center;
        }
        /* 展开后上部分伪类样式 */
        .active .upper::after {
            background: white;
            transform: rotate3d(1, 0, 0, 0deg);
        }
        /* 下部分样式 */
        .lower {
            transition: transform .8s ease;
            transition-delay: .35s;

        }
        /* 下部分伪类样式 */
        .lower::after {
            bottom: 100%;
            background: white;
            transform: rotate3d(1, 0, 0, 90deg);/*这里也是，在收据收起的时候，下部分伪类是沿着下边框绕X轴顺时针旋转90°的*/
            transform-origin: bottom center;
        }
         /* 展开后下部样式 */
        .active .lower  {
            border-top: none;
            transform: translateY(200px); /*需要沿Y轴向下位移200px才能显示出来*/
            transition-delay: 0s;
        }
        /* 展开后下部分伪类样式 */
        .active .lower::after {
            transform: rotate3d(1, 0, 0, 0deg);
        }
        h1 {
            margin: 10px 0 20px;	
        }
        .page-title {
            text-align: center;
            margin-bottom: 60px;
        }
        p{
            margin: 0; 
        }
        .sm {
            font-size: 80%;
        }
        button {
            width: 100%;
            padding: 15px 20px;
            background: none;
            outline: none;
            border: none;
            border-top: 1px dashed #dedede;
            color: #888;
            font-size: 90%;
            font-weight: 600;
            cursor: pointer;
            transition: all ease .35s;
        }

        button:hover {
            color: #999;
        }
        /* 折叠时中间收据数字的样式 */
        .receipt__content {
            display: flex;
            align-items: center;
            width: 100%;
            height: 200px;
            padding: 20px;
            position: absolute;
            top: 140px;
            left: 0;
            z-index: 1;
            pointer-events: none;
            opacity: 0;
            transform: translateY(-8px);
            transition: opacity ease .35s, transform ease .35s;
            transition-delay: 0;
        }
        /* 展开时中间收据数字样式显示 */
        .active .receipt__content {
            opacity: 1;
            transform: translateY(0);
            transition-delay: .8s;
        }

        table {
            width: 100%;
        }

        tr {
            display: flex;
            justify-content: space-between;
            margin: 10px 0;
        }
    </style>
</head>
<body>
        <h1 class="page-title">Receipt with 3D fold effect</h1>

        <article class="receipt">
            <!-- 上部分 -->
            <section class="receipt__half upper">
                <p>Receipt</p>
                <h1>$59.98</h1>
                <p class="sm">04.06.2019 - 09:47</p>
                <!-- 折叠部分 -->
                <div class="receipt__content">
                    <table>
                        <tbody>
                            <tr>
                                <td>Faded jeans</td>
                                <td>$39.99</td>
                            </tr>
                            <tr>
                                <td>Sleeveless shirt</td>
                                <td>$19.99</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </section>
            <!-- 下部分按钮 -->
            <section class="receipt__half lower">
                <button>More info</button>
            </section>
        </article>
        <script>
            const receipt = document.querySelector('.receipt')
            const button = document.querySelector('button')

            const toggleReceipt= () => {
                receipt.classList.toggle('active')//classList.toggle切换元素类名，如果类名存在，移除类名，不存在的话添加类名
                if(receipt.classList.contains('active')) {
                    //收据展开的时候
                    button.innerHTML = 'Less info'
                } else {
                    //收据折叠的时候
                    button.innerHTML = 'More info'
                }
            }
            button.addEventListener('click', toggleReceipt)
        </script>
</body>
</html>
```

个人认为这些属性大家一定都会，但是样式就是想象不出来，需要多积累、多使用、多想象、多编辑运行看效果之后总结下来就能设计出自己想要的立体样式了！