# CSS33D实现一个3D的按钮

我个人觉得这虽然是一个小小的按钮，但是其中的思想还是很有内涵的，先来一张图给大家展示一下效果，细节大家可以点击链接进行[查看效果详情](ref://./show/button.html)

![](/CSS33D/img/button/button.gif)

效果看完了，我们来看下这个精致的小按钮是怎么做到的，其中的主要思想就是用`input`去实现的，通过选择器`:checked`进行点击，旋转的是每一个`span`的伪类元素，细节看下面代码实现：
```
    <!-- html代码 -->
    <div class="center">
        <input type="checkbox" name="">
        <span>On</span>
        <span>Off</span>
    </div>
```
```
    body {
            margin: 0;
            padding: 0;
    }

    .center {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%,-50%);
        border: 2px solid #000;
        height: 40px;
        border-radius: 40px;
    }

    input[type="checkbox"] {
        position: relative;
        width: 100px;
        height: 40px;
        margin: 0;
        outline: none;
        cursor: pointer;
    }

    span {
        position: absolute;
        top: 0;
        width: 50%;
        height: 100%;
        line-height: 40px;
        height: 40px;
        text-align: center;
        text-transform: uppercase; /* 将小写字母变成大写字母*/
        font-weight: bold;
        pointer-events: none; /* 属性具体讲解在下方文章链接*/
        color:#fff;
    }

    span:nth-child(2) {
        left:0;
        border-top-left-radius: 40px;
        border-bottom-left-radius: 40px;
        background-color: #0f0;
    }

    span:nth-child(2):before {
        content:'';
        position:absolute;
        top:0;
        right:0;
        width: 100%;
        height: 100%;
        background: linear-gradient(90deg,#f00,#de0808);
        border-top-left-radius: 40px;
        border-bottom-left-radius: 40px;
        transform-origin: right;
        transition: 1s;
        backface-visibility: hidden; /* 旋转之后，背面隐藏*/
    }

    span:nth-child(3) {
        right:0;
        border-top-right-radius: 40px;
        border-bottom-right-radius: 40px;
        background-color: #f00;
    }
    
    input[type="checkbox"]:checked ~ span:nth-child(2):before {
        transform: rotateY(180deg); /* 点击旋转180°*/
    }

    span:nth-child(3):before {
        content:'';
        position:absolute;
        top:0;
        left:0;
        width: 100%;
        height: 100%;
        background: linear-gradient(90deg,#0cd20c,#0f0);
        border-top-right-radius: 40px;
        border-bottom-right-radius: 40px;
        transform-origin: left;
        transition: 1s;
        backface-visibility: hidden; /* 旋转之后，背面隐藏*/
        transform: rotateY(180deg); /* 点击旋转180°*/
    }

    input[type="checkbox"]:checked ~ span:nth-child(3):before {
        transform: rotateY(360deg); /* 这里旋转360°其实是相当于旋转了180°，因为在上面他已经旋转了180°进行抵消*/
    }
```

这个Demo中的一个属性`pointer-events: none;`是什么呢？

`pointer-events:none`顾名思意，就是鼠标事件拜拜的意思，元素应用了该属性，链接啊，点击啊什么的都变的不好使。但是它并不能阻止键盘事件的发生，我们给`<span>`设置了`pointer-events:none`意思是把span当成一个虚设的层，这样我们点击的时候，就可以直接点击到`<span>`下面的伪类元素进行相应的旋转操作了。

大家想了解更多关于这个属性的细节可以自行查看链接，详情请见[链接](https://www.zhangxinxu.com/wordpress/2011/12/css3-pointer-events-none-javascript/)，