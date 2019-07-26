# CSS33D实现折叠照片

> 参考原文来自周刊中2019年5月20日民哥分享的文章
> https://www.joshwcomeau.com/posts/folding-the-dom/ 

还是开门见山来个展示图给你们秀一下效果：

![](/CSS33D/img/fold-picture/fold-picture.gif)

是不是看起来还不错，哈哈，别小看这个动画，想想它还是有很多作用的，比如：作为图像的预加载器，图片一旦准备好就会展开，折叠的副本则可以使用base64编码版本，还可以作为点击查看图像时的加载动画，为常见功能添加很多异想天开的效果。有上一篇文章的内容作为基础，相信理解这篇文章也不是什么难的事情，让我们现在就开始吧！

首先看到这个折叠照片的动画不知道大家有没有什么想法，他是一整张图片？还是分开的，上面一半图片下面一半图片的拼凑？其实我觉得应该都可以实现吧，但是这里我们选取的上面一半图片，下面一半图片的方式来实现的，直接上代码为大家进行讲解~

```
// 首先来看动画效果是如何封装出来的

import React, { Component,useState }from 'react';
import './App.css';
import img from './img/img.jpg'

function Test ({ width, height }){
    const [
        foldAngle,
        setFoldAngle,
    ] = useState(0);//这里涉及到了React16.8新增的Hook，我在后续会进行详细的讲解，这里可以先简单理解为最开始我们向useState中传入折叠率的初始值为0，useState中的两个参数，第一个foldAngle为折叠率的当前状态，之后通过改变input来调用setFoldAngle函数来实时调整折叠率的大小
  
    //上半部分和下半部分共享的样式
    const sharedStyles = {
        width,
        height: height / 2, //上下高度都为原来高度的一半，之后进行拼凑出整张图片
    };

    return (
        <div style={{ perspective: 500,margin: 200}}> //设置了perspective视角为500，这里也可以设置perspective-origin视角原点来调整观看折叠效果的方向，没有设置就是默认视角在正前方进行观看

            {/* 上半部分 */}
            <div style={{
                ...sharedStyles,
                overflow: 'hidden',
                }}>
                    <img
                    src={img}
                    alt="a pick picture"
                    style={{
                        width,
                        height,
                    }}
                    />
            </div>

            {/* 下半部分 */}
            <div
                style={{
                ...sharedStyles,
                backgroundSize: `${width}px ${height}px`,
                backgroundImage: `url(${img})`,
                backgroundRepeat: 'no-repeat',
                backgroundPosition: `0px 100%`, //这里要说下这个background-position属性，这里设置的百分比是（容器的宽度/高度 - 图片的宽度/高度）* 百分比 之后得出的位置，比如在这里，下半部分容器高度140 - 图片高度280，等于-140，之后乘以100%为-140px,下半部分图片向上移动140px正好和上半部分图片拼接成一整张图片
                transform: `rotateX(${foldAngle}deg)`,
                transformOrigin: 'center top', //是容器进行旋转，所以是绕着容器的上边框进行旋转
                transformStyle: 'preserve-3d',
                }}
            >
                //旋转折叠之后背面朝向我们的时候为白色
                <div style={{
                    position: 'absolute',
                    top: 0, left: 0, right: 0, bottom: 0,
                    background: 'hsla(0, 100%, 100%, 0.9)', //这里用到了hsla，其实用我们熟悉的rgba完全可以实现比如:rgba（255，255，255，0.9）效果和hsla是一致的，既然说到了hsla那我们就来讲一下这个它，hsla的参数分别代表 H: Hue(色调) 0或者360代表红色，120代表绿色，240代表蓝色，也可以取其他值来指定颜色，取值为：0-360之间；S: Saturation(饱和度)，取值为：0.0%-100.0%之间；L: Lightness(亮度)，取值为：0.0%-100.0%之间；A：Alpha（透明度）取值为: 0-1之间。
                    backfaceVisibility: 'hidden', //backfaceVisibility属性默认值是visible：旋转的元素背面可见，hidden：旋转元素的背面不可见
                    transform: 'rotateX(180deg) translateZ(.5px)',//将白色的背景旋转180°至下半部分图片的背面，并且设置在Z轴上位移0.5px是为了防止折叠的时候白色背景闪烁，这样白色背景在Z轴上离我们的眼睛更近了一些，所以我们会看见覆盖在照片上的白色背景而不是下半部分的照片
                }}></div>
            </div>

            <br />
            <label htmlFor="slider">Fold ratio:</label>
            <input
                id="slider"
                type="range"
                min={0}
                max={180}
                value={foldAngle} //将当前值存在foldAngle中
                onChange={ev =>
                setFoldAngle(ev.target.value) //setFoldAngle函数将实时变化的值赋值给当前值
                }
                style={{ width }}
            />
        </div>
    );
}

class Foldable extends Component {
    constructor( props ) {
        super( props );
    }

    componentDidMount(){
        console.log(this.props.options)
    }
    render() {
        return(
            <Test {...this.props.options} />
        );
    }
}

export default Foldable;

```

通过对于代码的讲解，大家应该已经知道了这个折叠动画的编写思路，其中还详细介绍了背景图片位置设置百分比backgroundPosition、颜色设置hsla、背景图背面是否显示设置backfaceVisibility等，下面我和大家分享一下我对于那个听起来很高级的Hook的理解，可能不是很深入，也可能有理解不对的地方，希望大家如果有机会看到发现了问题要及时找我，我好改正并进一步学习。

> Hooks官方文档地址
> https://react.docschina.org/docs/hooks-intro.html

### 1.Hook简介

* Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

* Hook为什么会产生？

    第一个原因是我们经常维护一些组件，组件起初很简单，但是逐渐会被状态逻辑和副作用充斥。每个生命周期常常包含一些不相关的逻辑。在多数情况下，不可能将组件拆分为更小的粒度，因为状态逻辑无处不在。同时，这也是很多人将 React 与状态管理库结合使用的原因之一。但是，这往往会引入了很多抽象概念，需要你在不同的文件之间来回切换，使得复用变得更加困难。

    第二个原因是React 没有提供将可复用性行为“附加”到组件的途径（例如，把组件连接到 store）。React 需要为共享状态逻辑提供更好的原生途径。这个就促使了Hook的产生，Hook 使你在无需修改组件结构的情况下复用状态逻辑。

    第三个原因我们发现 class 是学习 React 的一大屏障。你必须去理解 JavaScript 中 this 的工作方式，还不能忘记绑定事件处理器。Hook解决了这个问题，Hook 使你在非 class 的情况下可以使用更多的 React 特性。

* Hook 是什么？ 

    Hook 是一个特殊的函数，它可以让你“钩入” React 的特性。例如，useState 是允许你在 React 函数组件中添加 state 的 Hook。

* 什么时候我们会用 Hook？

    如果你在编写函数组件并意识到需要向其添加一些 state，以前的做法是必须将其它转化为 class。现在你可以在现有的函数组件中使用 Hook。

* #### 最重要的是，Hook 和现有代码可以同时工作，你可以慢慢的渐进式地在项目中使用他们。

### 2.使用State Hook

我们来看下使用Hook和class的对比

``` 
    //使用Hook

    import React, { useState } from 'react';// 引入 React 中的 useState Hook。它让我们在函数组件中存储内部 state。

    function Example() {
        const [count, setCount] = useState(0);//在 Example 组件内部，我们通过调用 useState Hook 声明了一个新的 state 变量。它返回一对值给到我们命名的变量上。我们把变量命名为 count，因为它存储的是点击次数。我们通过传 0 作为 useState 唯一的参数来将其初始化为 0。第二个返回的值本身就是一个函数。它让我们可以更新 count 的值，所以我们叫它 setCount。

        return (
            <div>
            <p>You clicked {count} times</p>
            <button onClick={() => setCount(count + 1)}>//当用户点击按钮后，我们传递一个新的值给 setCount。React 会重新渲染 Example 组件，并把最新的 count 传给它。
                Click me
            </button>
            </div>
        );
    }
```

```
    //使用class

    class Example extends React.Component {
        constructor(props) {
            super(props);
            this.state = {  //state 初始值为 { count: 0 } 
            count: 0
            };
        }

        render() {
            return (
            <div>
                //当我们想在 class 中显示当前的 count，我们读取 this.state.count
                <p>You clicked {this.state.count} times</p>
                //当用户点击按钮后，我们通过调用 this.setState() 来增加 state.count。
                <button onClick={() => this.setState({ count: this.state.count + 1 })}>
                Click me
                </button>
            </div>
            );
        }
    }
```

### 3.使用Effect Hook

    此处没有涉及，后续有时间再进行讲解。。。