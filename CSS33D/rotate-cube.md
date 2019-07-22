# CSS33D实现旋转立方体

话不多说，先来个展示,如图：

![](/CSS33D/img/rotate-cube/rotate-cube.gif)

是不是看起来效果还不错，哈哈，是不是有点心动想知道怎么实现的？别着急，在实现之前我们需要了解一些CSS3的基础知识。

### 1.rotate（旋转）

rotate是旋转的意思，包括rotateX（绕X轴旋转）、rotateY（绕Y轴旋转）、rotateZ（绕Z轴旋转），其中X、Y大家都应该有所理解，Z轴其实就是垂直于屏幕，直戳眼睛的方向。看一下下图可以更好地帮助你理解：

![](/CSS33D/img/rotate-cube/coordinate1.png)

###translate（位移）

translate是位移的意思，包括translateX（X轴方向位移）、translateY（Y轴方向位移）、translateZ（Z轴方向位移）

###perspective（视角）

perspective是视角的意思，理解为眼睛距离屏幕的距离，它决定了你看到的是2D还是3D的图像。

###perspective-origin (视角原点)

perspective-origin是视角原点的意思，理解为眼睛视角的位置，perspective已经固定了眼睛距离屏幕的距离，perspective-origin就可以固定眼睛从哪个方向看物体

###transform-style: preserve-3d (3D透视)

transform-style属性是3D效果中经常使用的，其两个参数，flat以及preserve-3d. 前者flat为默认值，表示平面的；后者preserve-3d表示3D透视，一般而言，该声明应用在3D变换的元素们的父元素上。

###现在我们来上代码进行讲解

```
    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <meta http-equiv="X-UA-Compatible" content="ie=edge">
            <title>Document</title>
            <style lang="">
                * {
                    margin: 0;
                    padding: 0;
                }
                body {
                    background: #000;
                    perspective:1000px; //视角，眼睛离屏幕的距离
                    perspective-origin:50% 100px; //视角原点，眼睛看屏幕的方向
                }
                #contain{
                    position: relative;
                    width: 200px;
                    margin:200px auto;
                    transform-style: preserve-3d; //进行3D透视效果，给要进行3D变换的元素的父元素设置
                    animation:animate 3s linear infinite;
                }
                .cube {
                    width: 200px;
                    height: 200px;
                    background: rgba(255, 255, 255, 0.5);
                    font-size: 90px;
                    font-weight: bolder;
                    text-align: center;
                    line-height: 200px;
                    border-radius: 5px;
                    position: absolute;
                    border:1px solid #fff;
                }
                .one{
                    transform: rotateY(180deg) translateZ(100px);//绕Y轴顺时针旋转180°，之后沿Z轴向正方向(离我们进的方向)位移100px，这里要注意下，先旋转再位移和先位移再旋转是有区别的，方向需要注意
                }
                .two{
                    transform: rotateY(-90deg) translateZ(100px);//绕Y轴逆时针旋转90°，这里注意下，Y轴的正方向朝下，之后沿Z轴向正方向(离我们进的方向)位移100px
                }
                .three{
                    transform:rotateY(90deg) translateZ(100px);
                }
                .four{
                    transform: rotateX(90deg) translateZ(100px);
                }
                .five{
                    transform: rotateX(-90deg) translateZ(100px);
                }
                .six{
                    transform:translateZ(100px);
                }
                @keyframes animate {
                    from{

                    }to{
                        transform: rotateY(360deg);
                    }
                }
            </style>
        </head>

        <body>
            <div id="contain">
                <div class="cube one">1</div>
                <div class="cube two">2</div>
                <div class="cube three">3</div>
                <div class="cube four">4</div>
                <div class="cube five">5</div>
                <div class="cube six">6</div>
            </div>
        </body>
    </html>
```

###代码实践操作演示

通过上面对于代码的解读，对于之前涉及到的属性已经有了一定的了解，下面我们通过改变相应属性的值，看下3D效果的变化，让我们更好的理解

######1.将`perspective:1000px;`设置成`perspective:300px;`相当于我们在更近的距离去观察旋转的立方体，效果如下:
![](/CSS33D/img/rotate-cube/perspective.gif)

######2.将`perspective-origin:50% 100px;`设置成`perspective-origin:-500px 500px;`相当于我们在X轴负方向的500px和Y轴的正方向500px的地方也就是在左下角去看这个旋转立方体，效果如下:
![](/CSS33D/img/rotate-cube/perspective-origin.gif)

######3.将`transform-style: preserve-3d;`删除掉，不设置3D透视效果，显示如下：
![](/CSS33D/img/rotate-cube/transform-style.gif)

通过改变属性的参数进行实践，这样更好的理解了相关属性和Demo的逻辑，希望大家喜欢~