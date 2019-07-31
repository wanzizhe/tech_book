# CSS3实现照片飞入

这个例子的逻辑有点微微的复杂，交互很多，效果很棒，大家可以点击链接进行[查看效果](ref://./show/photo/index.html)，其中主要的效果就是24张小图片从左上角一个一个飞入，每一个呈现不同的随机旋转角度，点击每一张小图片会展现相应小图片的大图，可以进行左右图片的切换，切换图片的时候切换形式是马赛克的渐变，再次点击大图又会回到原来24张小图片打散的状态了。

#### 第一步我们需要写一个container容器将整个容器的画布铺好，其实它就是一张图片重复的铺盖

```
    /* css代码 */
    *{
        margin: 0;
        padding: 0;
    }
    body, html{
        width: 100%;
        height: 100%;
    }
    body{
        position: relative;
    }
    #container{
        width: 100%;
        height: 100%;
        background-image: url(img/bg.jpg);
    }

    <!-- html代码 -->
    <div id="container"></div>
```
#### 第二步我们需要做的就是预先加载这些图片，其中包括24张小图片以及24张大图片。

```     
    //js代码
    //声明了这个项目需要的所有变量
    var ROW = 4, COL = 6, NUM = ROW * COL,  
        //缩略图的宽度和高度，为了计算空隙大小
        THUMB_WIDTH = 125, THUMB_HEIGHT = 125,
        //大图片宽度和高度，用来散开和合上的计算
        BIG_WIDTH = 750, BIG_HEIGHT = 500;
        //记录图片加载数量
        var count = 0;
        //获取容器Dom
        var oContainer = document.getElementById("container");
        //左右按钮
        var oPrev = document.getElementById("prev");
        var oNext = document.getElementById("next");
        //大图片的时候也需要获取，所以要定义在外面
        var aImg;
        var nowIndex = 1;//保存的当前显示的图片，取值1~24

    //图片预加载
    for(var i=0; i<NUM; i++){
        //预加载小图
        var oThumbImg = new Image();
        //onload写在src前面，如果不这样可能会产生兼容性问题
        oThumbImg.onload = function(){
            //记录加载完的照片数量，每加载完一张就加加
            count++;
            if(count == NUM * 2){
                //虽然大小图片加载都调用了这个函数，但是只能执行一次，谁先加载完就先执行谁
                loadSuccess();
            }
        };
        //照片命名是有规律的 1-24
        oThumbImg.src = "img/thumbs/"+ (i + 1) +".jpg";

        //预加载大图
        var oBigImg = new Image();
        //onload写在src前面，如果不这样可能会产生兼容性问题
        oBigImg.onload = function(){
            //记录加载完的照片数量，每加载完一张就加加
            count++;
            if(count == NUM * 2){
                //虽然大小图片加载都调用了这个函数，但是只能执行一次，谁先加载完就先执行谁
                loadSuccess();
            }
        };
        //照片命名是有规律的 1-24
        oBigImg.src = "img/"+ (i + 1) +".jpg";
    }
```

#### 第三步图片加载成功之后，我们需要给每个图片加上相应的样式，并且设定从左上角进行一个一个的飞入，每张图片飞入到的位置是经过计算得来的，通过计算照片的宽高以及照片与照片之间的缝隙就可以算出每一张图片的准确位置，这里先说下从小图片变成大图片的样式转化，其中每一个小图片都是每个div的背景图片，而大图片是div下的span的背景图片，通过对于大图片进行背景图片定位而显示的（类似于精灵图的操作）

```
    /* css代码 */
    #container .img{
        width: 125px;
        height: 125px;
        border: 5px solid #fff;
        box-shadow: 2px 2px 2px 2px rgba(0, 0, 0, 0.5);  /* 水平、垂直、模糊程度、阴影大小、阴影颜色 */
        /* 最开始图片在最外面 */
        position: absolute;
        top: -500px;
        left: -500px;
        /* 照片飞入过度 */
        transition: all 1s ease;
        cursor: pointer;
    }

    //js代码
    //下面的变量在外面进行声明，内部进行赋值，这样在下面可以取到
    //记录加载的图片名称
    var index = 0;
    //照片的宽度
    var oImgWidth;
    //照片的高度
    var oImgHeight;
    //每一列的缝隙
    var fColGap;
    //每一行的缝隙
    var fRorGap;
    //所有图片加载完成之后执行的函数，小图作为div的背景图片，大图作为div下面的span的背景图片，所以点击之后大图的span会把小图覆盖
    function loadSuccess(){
        //创建24个div，背景图是小的缩略图
        for(var i=0; i<ROW; i++){
            for(var j=0; j<COL; j++){
                //记录加载的图片名称
                index++;
                var oDiv = document.createElement("div");
                oDiv.style.backgroundImage = "url(img/thumbs/"+ index +".jpg)";
                oDiv.className = "img";
                //在每个div中加入span，留着大图显示背景图片用
                oDiv.innerHTML = "<span></span>";
                //自定义属性是个对象，存储当前是哪个图片（比如：第三列的第二个{col: 3, row: 1}）
                oDiv.pos = {
                    col : j,
                    row : i
                };
                oDiv.index = index;//保存图片名称，（当前文件名字，留着之后用）
                //将动态创建好的div追加到oContainer中
                oContainer.appendChild(oDiv);
            }
        }

        //获取img Dom应该在这个加载成功的函数中，要不获取不到
        //计算空隙的大小
        aImg = document.getElementsByClassName("img");//24个div
        //图片的宽
        oImgWidth = aImg[0].offsetWidth;
        //图片的高
        oImgHeight = aImg[0].offsetHeight;
        //计算列空隙（思路：用总宽度减去所有图片的宽度之后再除以空隙的个数得到每一个空隙的宽度）
        fColGap = (oContainer.offsetWidth -  oImgWidth * COL) / (COL + 1);
        //计算行空隙
        fRorGap = (oContainer.offsetHeight - oImgHeight * ROW) / (ROW + 1);
        //计算每一个图片定位，一个一个飞入
        for(var i=23; i >= 0; i--){
            //延迟时间逆序（因为我们做的这个照片飞入是从最后一个开始的）
            aImg[i].style.transitionDelay = (NUM - i) * 100 + "ms";
            //计算每一个图片的位置（我将一个空隙和一张图片作为一个单位，那么第一张图片的位置就是一个空隙，第二章照片就是一个单位加上一个空隙）
            aImg[i].style.top = (fRorGap + oImgHeight) * aImg[i].pos.row + fRorGap + "px";
            aImg[i].style.left = (fColGap + oImgWidth) * aImg[i].pos.col + fColGap + "px";
            //每张图片的随机旋转角度，旋转角度为正负20°之间
            aImg[i].style.transform = "rotate("+ (Math.random() * 40 - 20) +"deg)";
        }
    }
```

#### 第四步，完成上面的步骤以后，初始的样式就已经展现出来了，现在我们要完成的是之后点击图片的交互，思路是我们首先要算出大图片在每一个span中的位置，之后将span的透明度从0设置成1，大图片就成功的覆盖了之前的小图片

```
    /* css代码 */
    /* span充满div */
    #container span{
        display: block;
        width: 100%;
        height: 100%;
        /* 一开始看不见 */
        opacity: 0;
        transition: all 1s ease;
    }

    //js代码
    //合上之后大图片两边的宽度
    var fBigRowGap = (oContainer.offsetHeight - BIG_HEIGHT) / 2;
    var fBigColGap = (oContainer.offsetWidth - BIG_WIDTH) / 2;
    var bFlag = true;//标志位，true表示当前是散开的要合上，false表示当前是合上的要散开
    //我们不可能给每一个小图片去绑定点击事件，所以就把小图片的点击事件委托给container绑定
    oContainer.onclick = function(e){
        //通过判断是this还是事件源来判断点击的是图片还是其他地方，因为只有点击图片的时候，才需要进行交互
        if(this === e.target){//e.target 为 oContainer或div或span    this是oContainer
            //点击空的时候，就是什么都不干
            return false;
        }
        //散开要合上
        if(bFlag){
            for(var i=0; i<aImg.length; i++){
                //大图片每一个图片的位置
                aImg[i].style.top = fBigRowGap + THUMB_HEIGHT * aImg[i].pos.row + "px";
                aImg[i].style.left = fBigColGap + THUMB_WIDTH * aImg[i].pos.col + "px";
                //合成大图片之后的一些样式改变
                aImg[i].style.transform = "rotate(0)";
                aImg[i].style.borderWidth = "1px";
                aImg[i].style.transitionDelay = "0ms";

                //找到每一个div下面的span
                var oSpan = aImg[i].getElementsByTagName("span")[0];
                oSpan.style.opacity = 1;
                //获取图片名称
                var imgURL;
                if(e.target.className == "img"){//div 点击图片边框的时候
                    imgURL = e.target.index;
                }else{//span  点击图片的时候
                    imgURL = e.target.parentNode.index;//img父元素的index
                }
                oSpan.style.backgroundImage = "url(img/"+ imgURL +".jpg)";
                //给每一个span这是背景图片定位
                oSpan.style.backgroundPosition = -THUMB_WIDTH * aImg[i].pos.col + "px "+ (-THUMB_HEIGHT * aImg[i].pos.row) +"px";
                //在点击span的时候要去掉延迟，只有点击左右的是有延迟
                oSpan.style.transitionDelay = "0ms";

                nowIndex = imgURL;//保存的当前显示的图片，取值1~24
            }
            //照片合上的时候显示按钮
            oPrev.style.display = oNext.style.display = "block";
        }else{
            //合上到散开
            for(var i=0; i<NUM; i++){
                //散开到原来位置
                aImg[i].style.top = (fRorGap + oImgHeight) * aImg[i].pos.row + fRorGap + "px";
                aImg[i].style.left = (fColGap + oImgWidth) * aImg[i].pos.col + fColGap + "px";
                //散开的样式
                aImg[i].style.transform = "rotate("+ (Math.random() * 40 - 20) +"deg)";
                aImg[i].style.borderWidth = "5px";
                var oSpan = aImg[i].getElementsByTagName("span")[0];
                oSpan.style.opacity = 0;
                oSpan.style.transitionDelay = "0ms";
            }
            //散开的时候按钮消失
            oPrev.style.display = oNext.style.display = "none";
        }
        //标示位取反
        bFlag = !bFlag;
    };
```

#### 第五步，现在已经完成的操作就是点击每个小图片显示相应的大图片，再点击大图片又散开成小图片，下面要做的操作是在大图片的时候，点击左右切换按钮切换上一张或者是下一张大图片

```
    /* css代码 */
    /* 左右按钮，相对于body定位 */
    #next, #prev{
        width: 60px;
        height: 60px;
        background-repeat: no-repeat;
        background-position: center;
        background-color: #fff;
        position: absolute;
        top: 50%;
        margin-top: -30px;
        display: none;
        cursor: pointer;
    }
    #prev{
        background-image: url(img/prev.png);
        left: 0;
        border-radius: 0 8px 8px 0;
    }
    #next{
        background-image: url(img/next.png);
        right: 0;
        border-radius: 8px 0 0 8px;
    }

    <!-- html代码 -->
    <div id="prev"></div>
    <div id="next"></div>

    //js代码
    //点击左右按钮切换图片，要知道当前是哪张图片
    oPrev.onclick = oNext.onclick = function(){
        if(this === oNext){
            //保存的当前显示的图片，取值1~24
            nowIndex++;
            if(nowIndex == NUM + 1){
                nowIndex = 1;
            }
        }else{
            nowIndex--;
            if(nowIndex == 0){
                nowIndex = NUM;
            }
        }
        //创建一个新的数组，然后进行相应的操作
        var arr = [];//0~23
        for(var i=0; i<NUM; i++){
            arr.push(i);
        }
        //输出随机排序的数组，这样可以形成马赛克切换效果
        arr.sort(function(a, b){
            return Math.random() - 0.5;
        });
        //将每一个span都切换成相应图片
        for(var i=0; i<arr.length; i++){
            //每一个img下的span
            var oSpan = aImg[arr[i]].getElementsByTagName("span")[0];
            //i+1是为了防止第一个切换的图片很快，一秒都不等
            oSpan.style.transitionDelay = (i + 1) * 50 + "ms";
            oSpan.style.backgroundImage = "url(img/"+ nowIndex +".jpg)";
        }
    };
```
到目前为止，整个项目就讲解完了，再回顾一下整个过程，首先我们将整个画布铺好，然后预加载24张小图片和24张大图片，在他们加载完成之后放在dom元素中，之后计算每一个图片的位置，然他们从最开始定位的左上角逆序飞入相应的位置，计算大图片在每一个span中的相应位置，给container绑定点击事件，通过标示位判断是合起来，还是散开，如果是合起来，判断点击位置的如果是小图片就显示相应的小图片div下面的span，如果是散开就还是恢复原来的位置，之后进行左右切换，通过sort和Math.random()方法创建随机排序的数组来制造马赛克切换的效果。
个人觉得效果很棒，完全不输3D的相关动画，所以学习记录了下来，有问题随时联系我，进行后续的问题补充~