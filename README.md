# parallax-scroll  

<h1>网页视差滚动效果</h1>

**目录**    
1. [利用CSS的background-attachment属性实现视差滚动（纯CSS方法）](#chapter1)  
2. [在1的基础上加上CSS3过度特效](#chapter2)  
3. [利用层次滚动速度的差异实现视差滚动（js方法）](#chapter3)  
4. [在3的基础上实现上下视差滚动](#chapter4)


<h3 id="chapter1">1. 利用CSS的background-attachment属性实现视差滚动</h3>

* <h5>我们来重新再来熟悉一下background属性（纯css方法）</h5>  
	`background-position`: 设置背景图像的起始位置  
    `background-repeat`: 设置是否及如何重复背景图像  
    `background-size`: 规定背景图像的尺寸  
    `background-attachment`: 设置背景图像是否固定或者随着页面的其余部分滚动  

	具体看[这里](http://www.w3school.com.cn/cssref/pr_background.asp)  
* <h5>重点来关注一下background-attachment属性</h5>  
	`background-attachment`有3个参数：  
	`scroll`：默认值。背景图像会随着页面其余部分的滚动而移动。  
	`fixed`:当页面的其余部分滚动时，背景图像不会移动。  
	`inherit`:规定应该从父元素继承 background-attachment 属性的设置。  

	我们就是要利用background-attachment: fixed；来实现视差滚动效果。大致步骤是：  
		1. 创建3个div，并分别给这3个div添加背景图（background-image）  
		2. 设置把3个背景图的`background-attachment`设置成`fixed`  
		代码：  
		<pre>
		    .section1,
		    .section2,
		    .section3 {
		      height: 800px;
		      width: 1000px;
		      margin: 0 auto;
		      font: 18px "Microsoft YaHei";
		      background-position: center;
		      background-repeat: no-repeat;
		      background-size: cover;  /* 把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。*/
		      background-attachment: fixed;  /** 这步是关键！！！ */
		    }
		    .section1 {
		      background-image: url("20.jpg");
		    }
		     .section2 {
		      background-image: url("30.jpg");
		    }
		    .section3 {
		      background-image: url("43.jpg");
		    } 
		</pre>

	具体看： [这里](https://allenmind.github.io/parallax-scroll/parallax.html)

<h3 id="chapter2">2. 在1的基础上加上CSS3过度特效</h3>

* <h5>简单来说，就是在scroll事件上添加一些动画事件： 滚动到页面某个值后会触发一个CSS3动画，这也是众多视差滚动中常见的一种。</h5>  
* <h5>首先来熟悉一下CSS3的过渡特效(transition属性)</h5>  
	看[这里](http://www.w3school.com.cn/cssref/pr_transition.asp)  
* <h5>具体结果和代码</h5>  
	看[这里](https://allenmind.github.io/parallax-scroll/parallax2.html)  

<h3 id="chapter3">3. 利用层次滚动速度的差异实现视差滚动（js方法）</h3>  

* <h5>来普及一波原理：</h5>  
	**让多层背景以不同的速度移动**，形成立体的运动效果，带来非常出色的视觉体验。  

	举个栗子：  
	在超级马里奥这个游戏中，当玩家操控马里奥移动时，  
	砖块和水管跟马里奥在同一层次（我们暂且称为“前景层”或“近景层”），这一层移动速度最块。  
	天上的白云为另一层次（称为“中间层”），移动速度中等。  
	背后的小山丘是最后一层（称为“背景层”），速度最慢。  
	三个层次按照不同的速度移动，就会产生一种立体的视差效果。  
* <h5>核心代码：</h5>  
	在页面滚动过程中，我们获取页面的scrollTop的值，根据不同参数值去设置各自scene的top值，这样滚动页面的时候，不同的速度就出来了
	<pre>
	    function onScroll() {
	      var scrollTop = document.documentElement.scrollTop || document.body.scrollTop; 
	      //  背景层、中间层、前景层按照不同的速度移动，从而产生视差效果
	      sceneBack.style.top = old_top1 + scrollTop*0.9 + "px"; //  背景层（速度最慢）
	      sceneCenter.style.top = old_top2 + scrollTop*0.5 + "px"; //中间层（速度中等）
	      sceneFront.style.top = old_top3 + scrollTop*0.3 + "px"; // 前景层（速度最快）
	    }
	</pre>  
* <h5>具体结果和代码</h5>  
	看[介里](https://allenmind.github.io/parallax-scroll/parallax3.html)

<h3 id="chapter4">4. 在3的基础上实现上下视差滚动</h3>  
“上下视差滚动”中的“上下”的意思就是，在页面向下滚动的过程中，有一些元素看上去是“向上”移动的，同时有另一些元素看上去是“向下”移动的。  

实现这个效果的原理和3中介绍的是一样的，只不过不是所有的元素都是往上升，而是一些元素上升，一些元素下沉。在计算top值的时候，不是“加上”，变成“减去”scrollTop就会有相应的效果。所以就有以下计算公式：  

<pre>
newTop1 = oldTop1 + scrollTop * x1 ;   (x1是个系数)
newTop2 = oldTop2 - scrollTop * x2 ;   (x2是个系数)
</pre>

这里要注意就是，如果我们想要两个元素在某一时刻滚动到`同一水平线`上的话，即要使：`newTop1 == newTop2`。加入oldTop1一开始为-1000px，oldTop2一开始为1000px，并要求滚动到1000px时，两个元素处在同一水平线上（即scrollTop为1000px），那么代入解下方程即可得到x1为2，x2为0。所以代码变为：  
<pre>
    function onScroll() {
      var scrollTop = document.documentElement.scrollTop || document.body.scrollTop; 
      //  按照不同的速度移动，从而产生视差效果
      img1.style.top = old_top1 + scrollTop*2 + "px"; //  图片1向上移动
      img2.style.top = old_top2 - scrollTop*0 + "px"; //  由于视差效果，当图片1向上移动，图片2只要按照原来速度（跟着滚动条滚动）即可营造视差效果，而且这样就可以把两个图片在某一时刻处于同一水平线上
    }
</pre>

具体效果和代码请看：  
[这里](https://allenmind.github.io/parallax-scroll/parallax4.html)
