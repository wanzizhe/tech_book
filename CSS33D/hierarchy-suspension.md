# CSS3实现3D分层悬浮效果

展示图片如下图所示，鼠标`hover`就会产生分层悬浮的效果，点击查看[展示链接](ref://./show/hierarchy-suspension/hierarchy-suspension.html)

![](/CSS33D/img/hierarchy-suspension/hierarchy-suspension.gif)

不知道大家看到这个效果有没有什么想法，受建中的启发，每看到一个效果先应该想一下如果是你自己做，你会怎么做，如何设计，带着自己的想法或者是实验结果去看人家是怎么做的，可以有更好的帮我理解，也加深了我们对于实践过程的记忆。

我看到这个效果之后，我的第一想法就是不会是四张透明度不同的照片定位做的吧，那也太傻了吧，咦~对，就可以通过css控制照片的透明度和位置来实现这个效果，果真不出所料，实例本身就是通过对于四张一样的照片进行透明度的变化进行层叠展示的，只不过实例本身运用了更多的css3的属性，不是通过定位而是通过`transform: translate`来位移的，然后通过`rotate``skew`来控制照片的角度的，来看下代码你一下子就明白啦~

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body{
            margin:0;
            padding:0;
            width:100%;
            height:100%;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .container{
            position: relative;
            width: 360px;
            height: 640px;
            margin-top:150px;
            background: rgba(0,0,0,.1); /* container是最下面一层，透明度设置为0.1就是灰白色的效果了 */
            transform: rotate(-30deg) skew(25deg) scale(0.8);/* skew扭曲，产生了照片现在的角度 */
            transition: 0.5s;
        }
        .container img{
            position:absolute;
            width:100%;
            transition: 0.5s;
        }
        .container:hover img:nth-child(4){
            transform: translate(160px, -160px);
            opacity: 1;
        }
        .container:hover img:nth-child(3){
            transform: translate(120px, -120px);
            opacity: 0.8;
        }
        .container:hover img:nth-child(2){
            transform: translate(80px, -80px);
            opacity: 0.6;
        }
        .container:hover img:nth-child(1){
            transform: translate(40px, -40px);
            opacity: 0.4;
        }
    </style>
</head>
<body>
    <div class="container">
        <img src="img/suspension.jpeg" alt="">
        <img src="img/suspension.jpeg" alt="">
        <img src="img/suspension.jpeg" alt="">
        <img src="img/suspension.jpeg" alt="">
    </div>
</body>
</html>
```

有问题随时联系我，我们一起共同努力进步^_^