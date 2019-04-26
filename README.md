<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
<meta name="referrer" content="origin" />
    <title>快速傅里叶变换(FFT)详解 - 自为风月马前卒 - 博客园</title>
<meta property="og:description" content="本文只讨论FFT在信息学奥赛中的应用 文中内容均为个人理解，如有错误请指出，不胜感激 前言 先解释几个比较容易混淆的缩写吧 DFT：离散傅里叶变换—&gt;$O(n^2)$计算多项式乘法 FFT：快速" />
	
    <link type="text/css" rel="stylesheet" href="/bundles/blog-common.css?v=svlZeZMvc3He7PqOD4T7SOuQn0_kIfLBYiy3idqd35Y1"/>
<link type="text/css" rel="stylesheet" href="/blog/customcss/330482.css?v=vA%2bD833DY7zo2MWAYtw3UYCtJog%3d"/>
<link id="mobile-style" media="only screen and (max-width: 767px)" type="text/css" rel="stylesheet" href="/skins/CodingLife/bundle-CodingLife-mobile.css?v=nSeK_P8BAqpQ4UDLpcwd6yl-jVYCXnR833BwkQ30uqQ1"/>

    <link title="RSS" type="application/rss+xml" rel="alternate" href="https://www.cnblogs.com/zwfymqz/rss"/>
    <link title="RSD" type="application/rsd+xml" rel="EditURI" href="https://www.cnblogs.com/zwfymqz/rsd.xml"/>
<link type="application/wlwmanifest+xml" rel="wlwmanifest" href="https://www.cnblogs.com/zwfymqz/wlwmanifest.xml"/>
    <script src="//common.cnblogs.com/scripts/jquery-2.2.0.min.js"></script>
    <script>var currentBlogId=330482;var currentBlogApp='zwfymqz',cb_enable_mathjax=true;var isLogined=true;</script>
    <script type="text/x-mathjax-config">
    MathJax.Hub.Config({
        tex2jax: { inlineMath: [['$','$'], ['\\(','\\)']], processClass: 'math', processEscapes: true },
        TeX: { 
            equationNumbers: { autoNumber: ['AMS'], useLabelIds: true }, 
            extensions: ['extpfeil.js'],
            Macros: {bm: "\\boldsymbol"}
        },
        'HTML-CSS': { linebreaks: { automatic: true } },
        SVG: { linebreaks: { automatic: true } }
        });
    </script><script src="//mathjax.cnblogs.com/2_7_2/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script src="/bundles/blog-common.js?v=OFBZTzkHKauV7mtIFLF9jlqrX-sesK9-mY9KjurLrq01" type="text/javascript"></script>
</head>
<body>
<a name="top"></a>

<!--PageBeginHtml Block Begin-->
<link rel="stylesheet" href="https://blog-static.cnblogs.com/files/zwfymqz/baguetteBox.min.css">
<script src="https://blog-static.cnblogs.com/files/zwfymqz/baguetteBox.min.js" async></script>
<script>
baguetteBox.run('.baguetteBoxThree', {
    animation: 'fadeIn',
    noScrollbars: true
});
</script>
<script type="text/javascript" src="https://libs.baidu.com/jquery/1.8.3/jquery.js"></script>
<script type="text/javascript" src="https://libs.baidu.com/jquery/1.8.3/jquery.min.js"></script>
		
<link href="https://files.cnblogs.com/files/widerg/nav.css" rel="stylesheet"></link>
<link href="https://cdn.bootcss.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet"></link>
<script src="https://cdn.bootcss.com/animejs/2.0.2/anime.min.js"></script>
<script src="https://files.cnblogs.com/files/widerg/utils.js?version=1.3"></script>
<script src="https://files.cnblogs.com/files/widerg/fireworks.js?version=1.3"></script>
<script src="https://files.cnblogs.com/files/zwfymqz/modernizr.custom.js"></script>
<script src="https://files.cnblogs.com/files/zwfymqz/dlmenu.js"></script>

<div id="dl-menu" class="dl-menuwrapper">
    <button class="dl-trigger">Open Menu</button>
    <ul class="dl-menu">
            <li><a href="http://attack204.com/" target="_blank" ><i aria-hidden="true" class="fa fa-globe fa-fw"></i>&nbsp;我的网站</a></li>
            <li><a href="http://zwfymqz.cnblogs.com/"><i aria-hidden="true" class="fa fa-home fa-fw"></i>&nbsp;首页</a></li>
            <li><a href="https://i.cnblogs.com/EditPosts.aspx?opt=1"><i aria-hidden="true" class="fa fa-pencil fa-fw"></i>&nbsp;新随笔</a></li>
            <li><a href="https://msg.cnblogs.com/send?recipient=%E8%87%AA%E4%B8%BA%E9%A3%8E%E6%9C%88%E9%A9%AC%E5%89%8D%E5%8D%92"><i aria-hidden="true" class="fa fa-link fa-fw"></i>&nbsp;联系</a></li>
            <li><a href="http://www.cnblogs.com/zwfymqz/rss"><i aria-hidden="true" class="fa fa-rss fa-fw"></i>&nbsp;订阅</a></li>
            <li><a href="https://i.cnblogs.com/"><i aria-hidden="true" class="fa fa-cog fa-fw"></i>&nbsp;管理</a></li>
    </ul>
</div>

<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?74ea64bad6ab0292a2fd9a69fac161cc";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>
<!--PageBeginHtml Block End-->

<!--done-->
<div id="home">
<div id="header">
	<div id="blogTitle">
	<a id="lnkBlogLogo" href="https://www.cnblogs.com/zwfymqz/"><img id="blogLogo" src="/Skins/custom/images/logo.gif" alt="返回主页" /></a>			
		
<!--done-->
<h1><a id="Header1_HeaderTitle" class="headermaintitle" href="https://www.cnblogs.com/zwfymqz/"><font color="#00FFFF">自</font><font color="#24DBDB">为</font><font color="#48B7B7">风</font><font color="#6C9393">月</font><font color="#906F6F">马</font><font color="#B44B4B">前</font><font color="#D82727">卒</font></a></h1>
<h2></h2>



		
	</div><!--end: blogTitle 博客的标题和副标题 -->
	<div id="navigator">
		
<ul id="navList">
<li></li>
<li><a id="blog_nav_myhome" class="menu" href="https://www.cnblogs.com/zwfymqz/">首页</a></li>
<li></li>
<li><a id="blog_nav_contact" class="menu" rel="nofollow" href="https://msg.cnblogs.com/send/%E8%87%AA%E4%B8%BA%E9%A3%8E%E6%9C%88%E9%A9%AC%E5%89%8D%E5%8D%92">联系</a></li>
<li><a id="blog_nav_rss" class="menu" href="https://www.cnblogs.com/zwfymqz/rss">订阅</a>
<!--<a id="blog_nav_rss_image" class="aHeaderXML" href="https://www.cnblogs.com/zwfymqz/rss"><img src="//www.cnblogs.com/images/xml.gif" alt="订阅" /></a>--></li>
<li><a id="blog_nav_admin" class="menu" rel="nofollow" href="https://i.cnblogs.com/">管理</a></li>
</ul>
		<div class="blogStats">
			
			<div id="blog_stats">
<span id="stats_post_count">随笔 - 1904&nbsp; </span>
<span id="stats_article_count">文章 - 4&nbsp; </span>
<span id="stats-comment_count">评论 - 960</span>
</div>
			
		</div><!--end: blogStats -->
	</div><!--end: navigator 博客导航栏 -->
</div><!--end: header 头部 -->

<div id="main">
	<div id="mainContent">
	<div class="forFlow">
		
        <div id="post_detail">
<!--done-->
<div id="topics">
	<div class = "post">
		<h1 class = "postTitle">
			<a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/zwfymqz/p/8244902.html">快速傅里叶变换(FFT)详解</a>
		</h1>
		<div class="clear"></div>
		<div class="postBody">
			<div id="cnblogs_post_body" class="blogpost-body"><p><span style="font-size: 18pt;">本文只讨论FFT在信息学奥赛中的应用</span></p>
<p><span style="font-size: 18pt;">文中内容均为个人理解，如有错误请指出，不胜感激</span></p>
<h2>前言</h2>
<p>先解释几个比较容易混淆的缩写吧</p>
<p>DFT：离散傅里叶变换&mdash;&gt;$O(n^2)$计算多项式乘法</p>
<p>FFT：快速傅里叶变换&mdash;&gt;$O(n*\log(n)$计算多项式乘法</p>
<p>FNTT/NTT：快速傅里叶变换的优化版&mdash;&gt;优化常数及误差</p>
<p>FWT：快速沃尔什变换&mdash;&gt;利用类似FFT的东西解决一类卷积问题</p>
<p>MTT：毛爷爷的FFT&mdash;&gt;非常nb/任意模数</p>
<p>FMT 快速莫比乌斯变化&mdash;&gt;感谢stump提供</p>
<h2>多项式</h2>
<h3>系数表示法</h3>
<p>设$A(x)$表示一个$n-1$次多项式</p>
<p>则$A(x)=\sum_{i=0}^{n} a_i * x^i$</p>
<p>例如：$A(3)=2+3*x+x^2$</p>
<p>利用这种方法计算多项式乘法复杂度为$O(n^2)$</p>
<p>（第一个多项式中每个系数都需要与第二个多项式的每个系数相乘）</p>
<h3>点值表示法</h3>
<p>将$n$互不相同的$x$带入多项式，会得到$n$个不同的取值$y$</p>
<p>则该多项式被这$n$个点$(x_1,y_1),(x_2,y_2),\dots,(x_n,y_n)$唯一确定</p>
<p>其中$y_i=\sum_{j=0}^{n-1} a_j*x_i^j$</p>
<p>例如：上面的例子用点值表示法可以为$(0,2),(1,6),(2,12)$</p>
<p>利用这种方法计算多项式乘法的时间复杂度仍然为$O(n^2)$</p>
<p>（选点$O(n)$，每次计算$O(n)$）</p>
<p>&nbsp;</p>
<p>我们可以看到，两种方法的时间复杂度都为$O(n^2)$，我们考虑对其进行优化</p>
<p>对于第一种方法，由于每个点的系数都是固定的，想要优化比较困难</p>
<p>对于第二种方法，貌似也没有什么好的优化方法，不过当你看完下面的知识，或许就不这么想了</p>
<p>&nbsp;</p>
<h2>复数</h2>
<p>在介绍复数之前，首先介绍一些可能会用到的东西</p>
<h3><span style="font-size: 18px;">向量</span></h3>
<p>同时具有大小和方向的量</p>
<p>在几何中通常用带有箭头的线段表示</p>
<h3><span style="font-size: 18px;">圆的弧度制</span></h3>
<p>等于半径长的圆弧所对的<a href="https://baike.baidu.com/item/%E5%9C%86%E5%BF%83%E8%A7%92" target="_blank">圆心角</a>叫做1<a href="https://baike.baidu.com/item/%E5%BC%A7%E5%BA%A6" target="_blank">弧度</a>的角，用符号rad表示，读作弧度。用弧度作单位来度量角的制度叫做弧度制</p>
<p>公式:</p>
<p>$1^{\circ }=\dfrac{\pi}{180}rad$</p>
<p>$180^{\circ }=\pi rad$</p>
<h3><span style="font-size: 18px;"><span style="font-size: 18px;">平行四边形定则</span></span></h3>
<p><span style="font-size: 18px;"><span style="font-size: 18px;"><img src="https://images2017.cnblogs.com/blog/1101696/201801/1101696-20180108214134535-1501092922.png" alt="" /></span></span></p>
<p>&nbsp;</p>
<p><span style="text-decoration: line-through;">(好像画的不是很标准。。)</span></p>
<p>平行四边形定则：AB+AD=AC</p>
<p>&nbsp;</p>
<h3>复数</h3>
<h4>定义</h4>
<p>设$a,b$为实数，$i^2=-1$，形如$a+bi$的数叫复数，其中$i$被称为虚数单位，复数域是目前已知最大的域</p>
<p>在复平面中，$x$代表实数，$y$轴（除原点外的点）代表虚数，从原点$(0,0)$到$(a,b)$的向量表示复数$a+bi$</p>
<p>模长：从原点$(0,0)$到点$(a,b)$的距离，即$\sqrt{a^2+b^2}$</p>
<p>幅角：假设以逆时针为正方向，从$x$轴正半轴到已知向量的转角的有向角叫做幅角</p>
<h3>运算法则</h3>
<p>加法：</p>
<p>因为在复平面中，复数可以被表示为向量，因此复数的加法与向量的加法相同，都满足平行四边形定则（就是上面那个）</p>
<p>乘法：</p>
<p>几何定义：复数相乘，模长相乘，幅角相加</p>
<p>代数定义：$$(a+bi)*(c+di)$$</p>
<p>$$=ac+adi+bci+bdi^2$$</p>
<p>$$=ac+adi+bci-bd$$</p>
<p>$$=(ac-bd)+(bc+ad)i$$</p>
<p>&nbsp;</p>
<h3>单位根</h3>
<p>下文中，默认$n$为$2$的正整数次幂</p>
<p>在复平面上，以原点为圆心，$1$为半径作圆，所得的圆叫<strong>单位圆</strong>。以圆点为起点，圆的$n$等分点为终点，做$n$个向量，设幅角为正且最小的向量对应的复数为$\omega_n$，称为$n$次单位根。</p>
<p>根据复数乘法的运算法则，其余$n-1$个复数为$\omega_n^2,\omega_n^3,\ldots,\omega_n^n$</p>
<p>注意$\omega_n^0=\omega_n^n=1$（对应复平面上以$x$轴为正方向的向量）</p>
<p>那么如何计算它们的值呢？这个问题可以由欧拉公式解决$$\omega_{n}^{k}=\cos\ k *\frac{2\pi}{n}+i\sin k*\frac{2\pi}{n}$$</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>例如</p>
<p>&nbsp;</p>
<p><span class="katex"><span class="katex-mathml"><span class="katex-html"><span class="strut" style="height: 0.64444em;"><span class="strut bottom" style="height: 0.64444em; vertical-align: 0em;"><span class="base textstyle uncramped"><span class="mord mathrm"><span class="katex"><span class="katex-mathml"><span class="katex-html"><span class="strut" style="height: 0.43056em;"><span class="strut bottom" style="height: 0.43056em; vertical-align: 0em;"><span class="base textstyle uncramped"><span class="mord mathit"><span class="katex"><span class="katex-mathml"><span class="katex-html"><span class="strut" style="height: 0.43056em;"><span class="strut bottom" style="height: 0.43056em; vertical-align: 0em;"><span class="base textstyle uncramped"><span class="mord mathit"><img src="https://images2017.cnblogs.com/blog/1101696/201801/1101696-20180109135920207-1872399773.png" alt="" width="260" height="234" /></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p>图中向量$AB$表示的复数为$8$次单位根</p>
<p>单位根的幅角为周角的$\frac{1}{n}$</p>
<blockquote>
<p>在代数中，若$z^n=1$，我们把$z$称为$n$次单位根</p>
</blockquote>
<h3>单位根的性质</h3>
<ul>
<li>$\omega _{n}^{k}=\cos k\dfrac{2\pi}{n}+i\sin k\dfrac {2\pi }{n}$（即上面的公式）</li>
</ul>
<ul>
<li>$\omega _{2n}^{2k}=\omega _{n}^{k}$</li>
</ul>
<p>证明：</p>
<p>$$\omega _{2n}^{2k}=\cos 2k*\frac{2\pi}{2n}+i\sin2k*\frac{2\pi}{2n}$$</p>
<p>$$=\omega _{n}^{k}$$</p>
<ul>
<li>$\omega _{n}^{k+\frac{n}{2}}=-\omega _{n}^{k}$</li>
</ul>
<p>$$\omega _{n}^{\frac{n}{2}}=\cos\frac{n}{2}*\frac{2\pi}{n}+i\sin\frac{n}{2}*\frac{2\pi}{n}$$</p>
<p>$$=\cos \pi+i\sin\pi$$</p>
<p>$$=-1$$</p>
<ul>
<li>$\omega _{n}^{0}=\omega _{n}^{n}=1$</li>
</ul>
<p>&nbsp;讲了这么多，貌似跟我们的正题没啥关系啊。。</p>
<p>&nbsp;OK！各位坐稳了，前方高能！</p>
<h2>快速傅里叶变换</h2>
<p>我们前面提到过，一个$n$次多项式可以被$n$个点唯一确定。</p>
<p>那么我们可以把单位根的$0$到$n-1$次幂带入，这样也可以把这个多项式确定出来。但是这样仍然是$O(n^2)$的呀！</p>
<p>我们设多项式$A(x)$的系数为$(a_o,a_1,a_2,\ldots,a_{n-1})$</p>
<p>那么$$A(x)=a_0+a_1*x+a_2*{x^2}+a_3*{x^3}+a_4*{x^4}+a_5*{x^5}+ \dots+a_{n-2}*x^{n-2}+a_{n-1}*x^{n-1}$$</p>
<p>将其下标按照奇偶性分类</p>
<p>$$A(x)=(a_0+a_2*{x^2}+a_4*{x^4}+\dots+a_{n-2}*x^{n-2})+(a_1*x+a_3*{x^3}+a_5*{x^5}+ \dots+a_{n-1}*x^{n-1})$$</p>
<p>设</p>
<p>&nbsp;</p>
<p>$$A_1(x)=a_0+a_2*{x}+a_4*{x^2}+\dots+a_{n-2}*x^{\frac{n}{2}-1}$$</p>
<p>$$A_2(x)=a_1+a_3*{x}+a_5*{x^2}+ \dots+a_{n-1}*x^{\frac{n}{2}-1}$$</p>
<p>那么不难得到</p>
<p>$$A(x)=A_1(x^2)+xA_2(x^2)$$</p>
<p>我们将$\omega_n^k (k&lt;\frac{n}{2}) $代入得</p>
<p>$$A(\omega_n^k)=A_1(\omega_n^{2k})+\omega_n^kA_2(\omega_n^{2k})$$</p>
<p>$$=A_1(\omega_{\frac{n}{2}}^{k})+\omega_n^kA_2(\omega_{\frac{n}{2}}^{k})$$</p>
<p>同理，将$\omega_n^{k+\frac{n}{2}}$代入得</p>
<p>$$A(\omega_n^{k+\frac{n}{2}})=A_1(\omega_n^{2k+n})+\omega_n^{k+\frac{n}{2}}(\omega_n^{2k+n})$$</p>
<p>$$=A_1(\omega_n^{2k}*\omega_n^n)-\omega_n^kA_2(\omega_n^{2k}*\omega_n^n)$$</p>
<p>$$=A_1(\omega_n^{2k})-\omega_n^kA_2(\omega_n^{2k})$$</p>
<p>&nbsp;</p>
<p>大家有没有发现什么规律？</p>
<p>没错！这两个式子只有一个常数项不同！</p>
<p>那么当我们在枚举第一个式子的时候，我们可以$O(1)$的得到第二个式子的值</p>
<p>又因为第一个式子的$k$在取遍$[0,\frac{n}{2}-1]$时，$k+\frac{n}{2}$取遍了$[\frac{n}{2},n-1]$</p>
<p>所以我们将原来的问题缩小了一半！</p>
<p>而缩小后的问题仍然满足原问题的性质，所以我们可以递归的去搞这件事情！</p>
<p>直到多项式仅剩一个常数项，这时候我们直接返回就好啦</p>
<p>&nbsp;</p>
<p>时间复杂度：</p>
<p>不难看出FFT是类似于线段树一样的分治算法。</p>
<p>因此它的时间复杂度为$O(nlogn)$</p>
<p>&nbsp;</p>
<h2>快速傅里叶逆变换</h2>
<p>不要以为FFT到这里就结束了。</p>
<p>我们上面的讨论是基于点值表示法的。</p>
<p>但是在平常的学习和研究中很少用点值表示法来表示一个多项式。</p>
<p>所以我们要考虑如何把点值表示法转换为系数表示法，这个过程叫做<strong>傅里叶逆变换</strong></p>
<p>&nbsp;</p>
<p>$(y_0,y_1,y_2,\dots,y_{n-1})$为$(a_0,a_1,a_2,\dots,a_{n-1})$的傅里叶变换（即点值表示）</p>
<p>设有另一个向量$(c_0,c_1,c_2,\dots,c_{n-1})$满足</p>
<p>$$c_k=\sum_{i=0}^{n-1}y_i(\omega_n^{-k})^i$$</p>
<p>即多项式$B(x)=y_0,y_1x,y_2x^2,\dots,y_{n-1}x^{n-1}$在$\omega_n^{0},\omega_n^{-1},\omega_n^{-2},\dots,\omega_{n-1}^{-(n-1)}$处的点值表示</p>
<p>emmmm又到推公式时间啦</p>
<p>$(c_0,c_1,c_2,\dots,c_{n-1})$满足<br />$$c_k=\sum_{i=0}^{n-1}y_i(\omega_n^{-k})^i$$</p>
<p>$$=\sum_{i=0}^{n-1}(\sum_{j=0}^{n-1}a_j(\omega_n^i)^j)(\omega_n^{-k})^i$$</p>
<p>$$=\sum_{i=0}^{n-1}(\sum_{j=0}^{n-1}a_j(\omega_n^j)^i)(\omega_n^{-k})^i$$</p>
<p>$$=\sum_{i=0}^{n-1}(\sum_{j=0}^{n-1}a_j(\omega_n^j)^i(\omega_n^{-k})^i)$$</p>
<p>$$=\sum_{i=0}^{n-1}\sum_{j=0}^{n-1}a_j(\omega_n^j)^i(\omega_n^{-k})^i$$</p>
<p>$$=\sum_{i=0}^{n-1}\sum_{j=0}^{n-1}a_j(\omega_n^{j-k})^i$$</p>
<p>$$=\sum_{j=0}^{n-1}a_j(\sum_{i=0}^{n-1}(\omega_n^{j-k})^i)$$</p>
<p>&nbsp;</p>
<p>设$S(x)=\sum_{i=0}^{n-1}x^i$</p>
<p>将$\omega_n^k$代入得</p>
<p>$$S(\omega_n^k)=1+(\omega_n^k)+(\omega_n^k)^2+\dots(\omega_n^k)^{n-1}$$</p>
<p>当$k!=0$时</p>
<p>等式两边同乘$\omega_n^k$得</p>
<p>$$\omega_n^kS(\omega_n^k)=\omega_n^k+(\omega_n^k)^2+(\omega_n^k)^3+\dots(\omega_n^k)^{n}$$</p>
<p>两式相减得</p>
<p>$$\omega_n^kS(\omega_n^k)-S(\omega_n^k)=(\omega_n^k)^{n}-1$$</p>
<p>$$S(\omega_n^k)=\frac{(\omega_n^k)^{n}-1}{\omega_n^k-1}$$</p>
<p>$$S(\omega_n^k)=\frac{(\omega_n^n)^{k}-1}{\omega_n^k-1}$$</p>
<p>$$S(\omega_n^k)=\frac{1-1}{\omega_n^k-1}$$</p>
<p>观察这个式子，不难看出它分母不为0，但是分子为0</p>
<p>因此，当$K!=0$时，$S(\omega^{k}_{n})=0$</p>
<p>那当$k=0$时呢？</p>
<p>很显然，$S(\omega^{0}_{n})=n$</p>
<p>&nbsp;</p>
<p>继续考虑刚刚的式子</p>
<p>$$c_k=\sum_{j=0}^{n-1}a_j(\sum_{i=0}^{n-1}(\omega_n^{j-k})^i)$$<br />当$j&nbsp;\neq&nbsp;k$时，值为$0$<br />当$j=k$时，值为$n$<br />因此，<br />$$c_k=na_k$$<br />$$a_k=\frac{c_k}{n}$$</p>
<p>这样我们就得到点值与系数之间的表示啦</p>
<p>&nbsp;</p>
<h2>理论总结</h2>
<p>至此，FFT的基础理论部分就结束了。</p>
<p>我们来小结一下FFT是怎么成功实现的</p>
<p>&nbsp;</p>
<p>首先，人们在用系数表示法研究多项式的时候遇阻</p>
<p>于是开始考虑能否用点值表示法优化这个东西。</p>
<p>然后根据复数的两条性质（这个思维跨度比较大）得到了一种分治算法。</p>
<p>最后又推了一波公式，找到了点值表示法与系数表示法之间转换关系。</p>
<p>&nbsp;</p>
<p>emmmm</p>
<p>其实FFT的实现思路大概就是</p>
<p>系数表示法&mdash;&gt;点值表示法&mdash;&gt;系数表示法</p>
<p>引用一下远航之曲大佬的图</p>
<p><span style="font-size: 18px;"><img src="https://images2017.cnblogs.com/blog/1101696/201802/1101696-20180211213536607-322408834.png" alt="" /></span></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>当然，在实现的过程中还有很多技巧</p>
<p>我们根据代码来理解一下</p>
<p>&nbsp;</p>
<h2>递归实现</h2>
<p>递归实现的方法比较简单。</p>
<p>就是按找我们上面说的过程，不断把要求的序列分成两部分，再进行合并</p>
<p>在c++的STL中提供了现成的complex类，但是我不建议大家用，毕竟手写也就那么几行，而且万一某个毒瘤卡STL那岂不是很ＧＧ？</p>
<p>&nbsp;</p>
<div class="cnblogs_code" onclick="cnblogs_code_show('f0457ffa-53ea-4dc4-b852-a4aa5a4d586e')"><img id="code_img_closed_f0457ffa-53ea-4dc4-b852-a4aa5a4d586e" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_f0457ffa-53ea-4dc4-b852-a4aa5a4d586e" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('f0457ffa-53ea-4dc4-b852-a4aa5a4d586e',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_f0457ffa-53ea-4dc4-b852-a4aa5a4d586e" class="cnblogs_code_hide">
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> luogu-judger-enable-o2
</span><span style="color: #008000;">//</span><span style="color: #008000;"> luogu-judger-enable-o2</span>
#include&lt;iostream&gt;<span style="color: #000000;">
#include</span>&lt;cstdio&gt;<span style="color: #000000;">
#include</span>&lt;cmath&gt;
<span style="color: #0000ff;">using</span> <span style="color: #0000ff;">namespace</span><span style="color: #000000;"> std;
</span><span style="color: #0000ff;">const</span> <span style="color: #0000ff;">int</span> MAXN = <span style="color: #800080;">4</span> * 1e6 + <span style="color: #800080;">10</span><span style="color: #000000;">;
inline </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> read() {
    </span><span style="color: #0000ff;">char</span> c = getchar(); <span style="color: #0000ff;">int</span> x = <span style="color: #800080;">0</span>, f = <span style="color: #800080;">1</span><span style="color: #000000;">;
    </span><span style="color: #0000ff;">while</span> (c &lt; <span style="color: #800000;">'</span><span style="color: #800000;">0</span><span style="color: #800000;">'</span> || c &gt; <span style="color: #800000;">'</span><span style="color: #800000;">9</span><span style="color: #800000;">'</span>) {<span style="color: #0000ff;">if</span> (c == <span style="color: #800000;">'</span><span style="color: #800000;">-</span><span style="color: #800000;">'</span>)f = -<span style="color: #800080;">1</span>; c =<span style="color: #000000;"> getchar();}
    </span><span style="color: #0000ff;">while</span> (c &gt;= <span style="color: #800000;">'</span><span style="color: #800000;">0</span><span style="color: #800000;">'</span> &amp;&amp; c &lt;= <span style="color: #800000;">'</span><span style="color: #800000;">9</span><span style="color: #800000;">'</span>) {x = x * <span style="color: #800080;">10</span> + c - <span style="color: #800000;">'</span><span style="color: #800000;">0</span><span style="color: #800000;">'</span>; c =<span style="color: #000000;"> getchar();}
    </span><span style="color: #0000ff;">return</span> x *<span style="color: #000000;"> f;
}
</span><span style="color: #0000ff;">const</span> <span style="color: #0000ff;">double</span> Pi = acos(-<span style="color: #800080;">1.0</span><span style="color: #000000;">);
</span><span style="color: #0000ff;">struct</span><span style="color: #000000;"> complex {
    </span><span style="color: #0000ff;">double</span><span style="color: #000000;"> x, y;
    complex (</span><span style="color: #0000ff;">double</span> xx = <span style="color: #800080;">0</span>, <span style="color: #0000ff;">double</span> yy = <span style="color: #800080;">0</span>) {x = xx, y =<span style="color: #000000;"> yy;}
} a[MAXN], b[MAXN];
complex </span><span style="color: #0000ff;">operator</span> + (complex a, complex b) { <span style="color: #0000ff;">return</span> complex(a.x + b.x , a.y +<span style="color: #000000;"> b.y);}
complex </span><span style="color: #0000ff;">operator</span> - (complex a, complex b) { <span style="color: #0000ff;">return</span> complex(a.x - b.x , a.y -<span style="color: #000000;"> b.y);}
complex </span><span style="color: #0000ff;">operator</span> * (complex a, complex b) { <span style="color: #0000ff;">return</span> complex(a.x * b.x - a.y * b.y , a.x * b.y + a.y * b.x);} <span style="color: #008000;">//</span><span style="color: #008000;">不懂的看复数的运算那部分</span>
<span style="color: #0000ff;">void</span> fast_fast_tle(<span style="color: #0000ff;">int</span> limit, complex *a, <span style="color: #0000ff;">int</span><span style="color: #000000;"> type) {
    </span><span style="color: #0000ff;">if</span> (limit == <span style="color: #800080;">1</span>) <span style="color: #0000ff;">return</span> ; <span style="color: #008000;">//</span><span style="color: #008000;">只有一个常数项</span>
    complex a1[limit &gt;&gt; <span style="color: #800080;">1</span>], a2[limit &gt;&gt; <span style="color: #800080;">1</span><span style="color: #000000;">];
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= limit; i += <span style="color: #800080;">2</span>) <span style="color: #008000;">//</span><span style="color: #008000;">根据下标的奇偶性分类</span>
        a1[i &gt;&gt; <span style="color: #800080;">1</span>] = a[i], a2[i &gt;&gt; <span style="color: #800080;">1</span>] = a[i + <span style="color: #800080;">1</span><span style="color: #000000;">];
    fast_fast_tle(limit </span>&gt;&gt; <span style="color: #800080;">1</span><span style="color: #000000;">, a1, type);
    fast_fast_tle(limit </span>&gt;&gt; <span style="color: #800080;">1</span><span style="color: #000000;">, a2, type);
    complex Wn </span>= complex(cos(<span style="color: #800080;">2.0</span> * Pi / limit) , type * sin(<span style="color: #800080;">2.0</span> * Pi / limit)), w = complex(<span style="color: #800080;">1</span>, <span style="color: #800080;">0</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;">Wn为单位根，w表示幂</span>
    <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; (limit &gt;&gt; <span style="color: #800080;">1</span>); i++, w = w * Wn) <span style="color: #008000;">//</span><span style="color: #008000;">这里的w相当于公式中的k</span>
        a[i] = a1[i] + w *<span style="color: #000000;"> a2[i],
               a[i </span>+ (limit &gt;&gt; <span style="color: #800080;">1</span>)] = a1[i] - w * a2[i]; <span style="color: #008000;">//</span><span style="color: #008000;">利用单位根的性质，O(1)得到另一部分</span>
<span style="color: #000000;">}
</span><span style="color: #0000ff;">int</span><span style="color: #000000;"> main() {
    </span><span style="color: #0000ff;">int</span> N = read(), M =<span style="color: #000000;"> read();
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= N; i++) a[i].x =<span style="color: #000000;"> read();
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= M; i++) b[i].x =<span style="color: #000000;"> read();
    </span><span style="color: #0000ff;">int</span> limit = <span style="color: #800080;">1</span>; <span style="color: #0000ff;">while</span> (limit &lt;= N + M) limit &lt;&lt;= <span style="color: #800080;">1</span><span style="color: #000000;">;
    fast_fast_tle(limit, a, </span><span style="color: #800080;">1</span><span style="color: #000000;">);
    fast_fast_tle(limit, b, </span><span style="color: #800080;">1</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;">后面的1表示要进行的变换是什么类型
    </span><span style="color: #008000;">//</span><span style="color: #008000;">1表示从系数变为点值
    </span><span style="color: #008000;">//</span><span style="color: #008000;">-1表示从点值变为系数
    </span><span style="color: #008000;">//</span><span style="color: #008000;">至于为什么这样是对的，可以参考一下c向量的推导过程，</span>
    <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= limit; i++<span style="color: #000000;">)
        a[i] </span>= a[i] *<span style="color: #000000;"> b[i];
    fast_fast_tle(limit, a, </span>-<span style="color: #800080;">1</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= N + M; i++) printf(<span style="color: #800000;">"</span><span style="color: #800000;">%d </span><span style="color: #800000;">"</span>, (<span style="color: #0000ff;">int</span>)(a[i].x / limit + <span style="color: #800080;">0.5</span>)); <span style="color: #008000;">//</span><span style="color: #008000;">按照我们推倒的公式，这里还要除以n</span>
    <span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
}</span></pre>
</div>
<span class="cnblogs_code_collapse">递归版</span></div>
<p>&nbsp;</p>
<p><span style="font-size: 18px;">update:递归版我本地是可以AC的，只要开大数组就可以了，但是交到洛谷上会WA0</span></p>
<p>&nbsp;</p>
<p><img src="https://images2017.cnblogs.com/blog/1101696/201802/1101696-20180212073744202-1563772554.png" alt="" /></p>
<p>&nbsp;</p>
<p>woc? <span style="text-decoration: line-through;">&nbsp;脸好疼。。。。。。</span></p>
<p>咳咳。</p>
<p>速度什么的才不是关键呢？</p>
<p>关键是我们AC不了啊啊啊</p>
<p>表着急，AC不了不代表咱们的算法不对，只能说这种实现方法太low了</p>
<p>下面介绍一种更高效的方法</p>
<p>&nbsp;</p>
<h2>迭代实现</h2>
<p><span style="text-decoration: line-through;">再盗一下那位大佬的图</span></p>
<p><img src="https://images2017.cnblogs.com/blog/1101696/201802/1101696-20180212074250859-1560811086.png" alt="" width="420" height="397" /></p>
<p>&nbsp;</p>
<p>观察一下原序列和反转后的序列？</p>
<p>聪明的你有没有看出什么显而易见的性质？</p>
<p>没错！</p>
<p>我们需要求的序列实际是原序列下标的二进制反转！</p>
<p>因此我们对序列按照下标的奇偶性分类的过程其实是没有必要的</p>
<p>&nbsp;</p>
<p>这样我们可以$O(n)$的利用某种操作得到我们要求的序列，然后不断向上合并就好了</p>
<p>&nbsp;</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> luogu-judger-enable-o2</span>
#include&lt;iostream&gt;<span style="color: #000000;">
#include</span>&lt;cstdio&gt;<span style="color: #000000;">
#include</span>&lt;cmath&gt;
<span style="color: #0000ff;">using</span> <span style="color: #0000ff;">namespace</span><span style="color: #000000;"> std;
</span><span style="color: #0000ff;">const</span> <span style="color: #0000ff;">int</span> MAXN = 1e7 + <span style="color: #800080;">10</span><span style="color: #000000;">;
inline </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> read() {
    </span><span style="color: #0000ff;">char</span> c = getchar(); <span style="color: #0000ff;">int</span> x = <span style="color: #800080;">0</span>, f = <span style="color: #800080;">1</span><span style="color: #000000;">;
    </span><span style="color: #0000ff;">while</span> (c &lt; <span style="color: #800000;">'</span><span style="color: #800000;">0</span><span style="color: #800000;">'</span> || c &gt; <span style="color: #800000;">'</span><span style="color: #800000;">9</span><span style="color: #800000;">'</span>) {<span style="color: #0000ff;">if</span> (c == <span style="color: #800000;">'</span><span style="color: #800000;">-</span><span style="color: #800000;">'</span>)f = -<span style="color: #800080;">1</span>; c =<span style="color: #000000;"> getchar();}
    </span><span style="color: #0000ff;">while</span> (c &gt;= <span style="color: #800000;">'</span><span style="color: #800000;">0</span><span style="color: #800000;">'</span> &amp;&amp; c &lt;= <span style="color: #800000;">'</span><span style="color: #800000;">9</span><span style="color: #800000;">'</span>) {x = x * <span style="color: #800080;">10</span> + c - <span style="color: #800000;">'</span><span style="color: #800000;">0</span><span style="color: #800000;">'</span>; c =<span style="color: #000000;"> getchar();}
    </span><span style="color: #0000ff;">return</span> x *<span style="color: #000000;"> f;
}
</span><span style="color: #0000ff;">const</span> <span style="color: #0000ff;">double</span> Pi = acos(-<span style="color: #800080;">1.0</span><span style="color: #000000;">);
</span><span style="color: #0000ff;">struct</span><span style="color: #000000;"> complex {
    </span><span style="color: #0000ff;">double</span><span style="color: #000000;"> x, y;
    complex (</span><span style="color: #0000ff;">double</span> xx = <span style="color: #800080;">0</span>, <span style="color: #0000ff;">double</span> yy = <span style="color: #800080;">0</span>) {x = xx, y =<span style="color: #000000;"> yy;}
} a[MAXN], b[MAXN];
complex </span><span style="color: #0000ff;">operator</span> + (complex a, complex b) { <span style="color: #0000ff;">return</span> complex(a.x + b.x , a.y +<span style="color: #000000;"> b.y);}
complex </span><span style="color: #0000ff;">operator</span> - (complex a, complex b) { <span style="color: #0000ff;">return</span> complex(a.x - b.x , a.y -<span style="color: #000000;"> b.y);}
complex </span><span style="color: #0000ff;">operator</span> * (complex a, complex b) { <span style="color: #0000ff;">return</span> complex(a.x * b.x - a.y * b.y , a.x * b.y + a.y * b.x);} <span style="color: #008000;">//</span><span style="color: #008000;">不懂的看复数的运算那部分</span>
<span style="color: #0000ff;">int</span><span style="color: #000000;"> N, M;
</span><span style="color: #0000ff;">int</span><span style="color: #000000;"> l, r[MAXN];
</span><span style="color: #0000ff;">int</span> limit = <span style="color: #800080;">1</span><span style="color: #000000;">;
</span><span style="color: #0000ff;">void</span> fast_fast_tle(complex *A, <span style="color: #0000ff;">int</span><span style="color: #000000;"> type) {
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; limit; i++<span style="color: #000000;">)
        </span><span style="color: #0000ff;">if</span> (i &lt; r[i]) swap(A[i], A[r[i]]); <span style="color: #008000;">//</span><span style="color: #008000;">求出要迭代的序列</span>
    <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> mid = <span style="color: #800080;">1</span>; mid &lt; limit; mid &lt;&lt;= <span style="color: #800080;">1</span>) { <span style="color: #008000;">//</span><span style="color: #008000;">待合并区间的长度的一半</span>
        complex Wn( cos(Pi / mid) , type * sin(Pi / mid) ); <span style="color: #008000;">//</span><span style="color: #008000;">单位根</span>
        <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> R = mid &lt;&lt; <span style="color: #800080;">1</span>, j = <span style="color: #800080;">0</span>; j &lt; limit; j += R) { <span style="color: #008000;">//</span><span style="color: #008000;">R是区间的长度，j表示前已经到哪个位置了</span>
            complex w(<span style="color: #800080;">1</span>, <span style="color: #800080;">0</span>); <span style="color: #008000;">//</span><span style="color: #008000;">幂</span>
            <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> k = <span style="color: #800080;">0</span>; k &lt; mid; k++, w = w * Wn) { <span style="color: #008000;">//</span><span style="color: #008000;">枚举左半部分</span>
                complex x = A[j + k], y = w * A[j + mid + k]; <span style="color: #008000;">//</span><span style="color: #008000;">蝴蝶效应</span>
                A[j + k] = x +<span style="color: #000000;"> y;
                A[j </span>+ mid + k] = x -<span style="color: #000000;"> y;
            }
        }
    }
}
</span><span style="color: #0000ff;">int</span><span style="color: #000000;"> main() {
    </span><span style="color: #0000ff;">int</span> N = read(), M =<span style="color: #000000;"> read();
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= N; i++) a[i].x =<span style="color: #000000;"> read();
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= M; i++) b[i].x =<span style="color: #000000;"> read();
    </span><span style="color: #0000ff;">while</span> (limit &lt;= N + M) limit &lt;&lt;= <span style="color: #800080;">1</span>, l++<span style="color: #000000;">;
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; limit; i++<span style="color: #000000;">)
        r[i] </span>= ( r[i &gt;&gt; <span style="color: #800080;">1</span>] &gt;&gt; <span style="color: #800080;">1</span> ) | ( (i &amp; <span style="color: #800080;">1</span>) &lt;&lt; (l - <span style="color: #800080;">1</span><span style="color: #000000;">) ) ;
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 在原序列中 i 与 i/2 的关系是 ： i可以看做是i/2的二进制上的每一位左移一位得来
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 那么在反转后的数组中就需要右移一位，同时特殊处理一下奇数</span>
    fast_fast_tle(a, <span style="color: #800080;">1</span><span style="color: #000000;">);
    fast_fast_tle(b, </span><span style="color: #800080;">1</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= limit; i++) a[i] = a[i] *<span style="color: #000000;"> b[i];
    fast_fast_tle(a, </span>-<span style="color: #800080;">1</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt;= N + M; i++<span style="color: #000000;">)
        printf(</span><span style="color: #800000;">"</span><span style="color: #800000;">%d </span><span style="color: #800000;">"</span>, (<span style="color: #0000ff;">int</span>)(a[i].x / limit + <span style="color: #800080;">0.5</span><span style="color: #000000;">));
    </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
}</span></pre>
</div>
<p>&nbsp;</p></div><div id="MySignature"></div>
<div class="clear"></div>
<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>
<div id="EntryTag"></div>
<div id="blog_post_info">
</div>
<div class="clear"></div>
<div id="post_next_prev"></div>
</div>


		</div>
		<div class = "postDesc">posted @ <span id="post-date">2018-02-11 18:53</span> <a href='https://www.cnblogs.com/zwfymqz/'>自为风月马前卒</a> 阅读(<span id="post_view_count">...</span>) 评论(<span id="post_comment_count">...</span>)  <a href ="https://i.cnblogs.com/EditPosts.aspx?postid=8244902" rel="nofollow">编辑</a> <a href="#" onclick="AddToWz(8244902);return false;">收藏</a></div>
	</div>
	<script type="text/javascript">var allowComments=true,cb_blogId=330482,cb_entryId=8244902,cb_blogApp=currentBlogApp,cb_blogUserGuid='f9ae130e-24ec-e611-845c-ac853d9f53ac',cb_entryCreatedDate='2018/2/11 18:53:00';loadViewCount(cb_entryId);var cb_postType=1;var isMarkdown=false;</script>
	
</div><!--end: topics 文章、评论容器-->
</div><a name="!comments"></a><div id="blog-comments-placeholder"></div><script type="text/javascript">var commentManager = new blogCommentManager();commentManager.renderComments(0);</script>
<div id='comment_form' class='commentform'>
<a name='commentform'></a>
<div id='divCommentShow'></div>
<div id='comment_nav'><span id='span_refresh_tips'></span><a href='javascript:void(0);' onclick='return RefreshCommentList();' id='lnk_RefreshComments' runat='server' clientidmode='Static'>刷新评论</a><a href='#' onclick='return RefreshPage();'>刷新页面</a><a href='#top'>返回顶部</a></div>
<div id='comment_form_container'></div>
<div class='ad_text_commentbox' id='ad_text_under_commentbox'></div>
<div id='ad_t2'></div>
<div id='opt_under_post'></div>
<script async='async' src='https://www.googletagservices.com/tag/js/gpt.js'></script>
<script>
  var googletag = googletag || {};
  googletag.cmd = googletag.cmd || [];
</script>
<script>
  googletag.cmd.push(function() {
        googletag.defineSlot('/1090369/C1', [300, 250], 'div-gpt-ad-1546353474406-0').addService(googletag.pubads());
        googletag.defineSlot('/1090369/C2', [468, 60], 'div-gpt-ad-1539008685004-0').addService(googletag.pubads());
        googletag.pubads().enableSingleRequest();
        googletag.enableServices();
  });
</script>
<div id='cnblogs_c1' class='c_ad_block'>
    <div id='div-gpt-ad-1546353474406-0' style='height:250px; width:300px;'></div>
</div>
<div id='under_post_news'></div>
<div id='cnblogs_c2' class='c_ad_block'>
    <div id='div-gpt-ad-1539008685004-0' style='height:60px; width:468px;'></div>
</div>
<div id='under_post_kb'></div>
<div id='HistoryToday' class='c_ad_block'></div>
<script type='text/javascript'>
 if(enablePostBottom()) {
    codeHighlight();
    fixPostBody();
    setTimeout(function () { incrementViewCount(cb_entryId); }, 50);
    deliverT2();
    deliverC1();
    deliverC2();    
    loadNewsAndKb();
    loadBlogSignature();
    LoadPostInfoBlock(cb_blogId, cb_entryId, cb_blogApp, cb_blogUserGuid);
    GetPrevNextPost(cb_entryId, cb_blogId, cb_entryCreatedDate, cb_postType);
    loadOptUnderPost();
    GetHistoryToday(cb_blogId, cb_blogApp, cb_entryCreatedDate);  
}
</script>
</div>

    
	</div><!--end: forFlow -->
	</div><!--end: mainContent 主体内容容器-->

	<div id="sideBar">
		<div id="sideBarMain">
			
<!--done-->
<div class="newsItem">
<h3 class="catListTitle">公告</h3>
	<div id="blog-news"></div><script type="text/javascript">loadBlogNews();</script>
</div>

			<div id="blog-calendar" style="display:none"></div><script type="text/javascript">loadBlogDefaultCalendar();</script>
			
			<div id="leftcontentcontainer">
				<div id="blog-sidecolumn"></div><script type="text/javascript">loadBlogSideColumn();</script>
			</div>
			
		</div><!--end: sideBarMain -->
	</div><!--end: sideBar 侧边栏容器 -->
	<div class="clear"></div>
	</div><!--end: main -->
	<div class="clear"></div>
	<div id="footer">
		
<!--done-->
Copyright &copy;2019 自为风月马前卒
	</div><!--end: footer -->
</div><!--end: home 自定义的最大容器 -->

<!--PageEndHtml Block Begin-->
<div class="autoMenu" id="autoMenu" data-autoMenu></div>



<script type="text/javascript" src="https://blog-static.cnblogs.com/files/jackson0714/Comments.js"></script>
<script language="javascript" type="text/javascript">
    if (document.location.href.match(/cnblogs\.com\/[^\/]+\/(p|articles)\/[0-9]+\.html/)) {
        $("#sideBar").hide();
    }
  
</script>
<script language="javascript" type="text/javascript">
    // 生成目录索引列表
    // ref: http://www.cnblogs.com/wangqiguo/p/4355032.html
    // modified by: zzq
    function GenerateContentList() {
        var mainContent = $('#cnblogs_post_body');
        var h2_list = $('#cnblogs_post_body h2');//如果你的章节标题不是h2,只需要将这里的h2换掉即可

        if (mainContent.length < 1)
            return;

        if (h2_list.length > 0) {
            var content = '<a name="_labelTop"></a>';
            content += '<div id="navCategory" style="color:#152e97;">';
            content += '<p style="font-size:18px;"><b>目录</b></p>';
            content += '<ul>';
            for (var i = 0; i < h2_list.length; i++) {
                var go_to_top = '<div style="text-align: right;"><a href="#_labelTop" style="color:#f68a33">回到顶部</a><a name="_label' + i + '"></a></div>';
                $(h2_list[i]).before(go_to_top);

                var h3_list = $(h2_list[i]).nextAll("h3");
                var li3_content = '';
                for (var j = 0; j < h3_list.length; j++) {
                    var tmp = $(h3_list[j]).prevAll('h2').first();
                    if (!tmp.is(h2_list[i]))
                        break;
                    var li3_anchor = '<a name="_label' + i + '_' + j + '"></a>';
                    $(h3_list[j]).before(li3_anchor);
                    li3_content += '<li><a href="#_label' + i + '_' + j + '">' + $(h3_list[j]).text() + '</a></li>';
                }

                var li2_content = '';
                if (li3_content.length > 0)
                    li2_content = '<li><a href="#_label' + i + '">' + $(h2_list[i]).text() + '</a><ul>' + li3_content + '</ul></li>';
                else
                    li2_content = '<li><a href="#_label' + i + '">' + $(h2_list[i]).text() + '</a></li>';
                content += li2_content;
            }
            content += '</ul>';
            content += '</div><p>&nbsp;</p>';
            content += '<hr style="height:1px;border:none;border-top:1px dashed #0066CC;"/>';
            if ($('#cnblogs_post_body').length != 0) {
                $($('#cnblogs_post_body')[0]).prepend(content);
            }
        }
    }
    GenerateContentList();
</script>




<style>
    #back-top {
        position: fixed;
        bottom: 10px;
        right: 5px;
        z-index: 99;
    }

    #back-top span {
        width: 56px;
        height: 170px;
        display: block;
        background: url(http://images.cnblogs.com/cnblogs_com/seanshao/855033/o_rocket.png)no-repeat center center;
    }

    #back-top a {
        outline: none
    }
</style>
<script type="text/javascript">
    $(function () {
        // hide #back-top first
        $("#back-top").hide();
        // fade in #back-top
        $(window).scroll(function () {
            if ($(this).scrollTop() > 500) {
                $('#back-top').fadeIn();
            } else {
                $('#back-top').fadeOut();
            }
        });
        // scroll body to 0px on click
        $('#back-top a').click(function () {
            $('body,html').animate({
                scrollTop: 0
            }, 800);
            return false;
        });
    });
</script>
<p id="back-top" style="display:none"><a href="#top"><span></span></a></p>








<div id="customFooter">
    <h1 class="footer-title">Contact with me</h1>
    <ul>
        <li><a href="https://weibo.com/u/5379824760" target="_blank"><i aria-hidden="true"
                    class="fa fa-weibo fa-fw"></i>weibo</a></li>
        <li><a href="https://github.com/attack204" target="_blank"><i aria-hidden="true"
                    class="fa fa-github fa-fw"></i>Github</a></li>
        <li><a href="http://wpa.qq.com/msgrd?v=3&uin=757394026&site=qq&menu=yes" target="_blank"><i aria-hidden="true"
                    class="fa fa-qq fa-fw"></i>QQ</a></li>
        <li><a href="mailto:757394026@qq.com" target="_blank"><i aria-hidden="true"
                    class="fa fa-envelope fa-fw"></i>Email</a></li>
    </ul>
    <p id="copyright">Copyright 2018 自为风月马前卒</p>
</div>


<script type="text/javascript">
    $(function () {
        $('#dl-menu').dlmenu({
            animationClasses: {
                classin: 'dl-animate-in-3',
                classout: 'dl-animate-out-3'
            }
        });
    });

    (function (a) { // 控制台输出信息
        if (!a) return;
        var msg = "%c欢迎访问自为风月马前卒的博客,\n鄙人蒟蒻,望各位大佬多加指点.\n                             ------http://zwfymqz.cnblogs.com/";
    })(top.console);



    function createLink(URL, lnkId, charset, media) {
        var head = document.getElementsByTagName('head')[0],
            linkTag = null;

        if (!URL) {
            return false;
        }

        linkTag = document.createElement('link');
        linkTag.setAttribute('rel', 'shortcut icon');
        linkTag.setAttribute('type', 'image/x-icon');
        linkTag.href = URL;
        head.appendChild(linkTag);
    };
    createLink('http://cdn.attack204.com/%E5%8D%9A%E5%AE%A2%E5%9B%ADicon.png');
    //$("body").prepend($("<canvas class='fireworks'></canvas>"));
    //用来在head标签中添加link标签

    jQuery.fn.wait = function (func, times, interval) {
        var _times = times || -1, //100次
            _interval = interval || 20, //20毫秒每次
            _self = this,
            _selector = this.selector, //选择器
            _iIntervalID; //定时器id
        if (this.length) { //如果已经获取到了，就直接执行函数
            func && func.call(this);
        } else {
            _iIntervalID = setInterval(function () {
                if (!_times) { //是0就退出
                    clearInterval(_iIntervalID);
                }
                _times <= 0 || _times--; //如果是正数就 --

                _self = $(_selector); //再次选择
                if (_self.length) { //判断是否取到
                    func && func.call(_self);
                    clearInterval(_iIntervalID);
                }
            }, _interval);
        }
        return this;
    };
    $("#sideBar #p_b_follow a").wait(function () {
        $("#sideBar #p_b_follow :contains('+加关注')").html("<i class=\"fa fa-heart\" aria-hidden=\"true\"></i>&nbsp;关注");
    });
</script>



<script src="https://blog-static.cnblogs.com/files/zwfymqz/spig8.js"></script>
<!--博客精灵 start*-->
<div id="spig" class="spig">
    <div id="message">……</div>
    <div id="mumu" class="mumu"></div>
</div>

<script type="text/javascript">
    var isindex = true;
    var title = "";
    var visitor = "这位怪蜀黍";
</script>
<!--精灵end*-->
<!--PageEndHtml Block End-->
</body>
</html>
