# PostCSS自学笔记（二）【番外篇二】

这次我继续研究PostCSS的插件的执行顺序，

之前有研究过做过假设，在插件列表中，PostCSS的插件执行顺序自上而下，一切看起来似乎是没有任何问题的。

我也看过有关PostCSS解析器的相关文章，有以下一些收获：

![该作者理解的模型](https://segmentfault.com/img/bVqy8j)

摘自：[PostCSS 是个什么鬼东西？](https://segmentfault.com/a/1190000003909268)

当然这张图并没有对Plugin System进行解释😀。

再有：

![PostCSS 运行机制](http://www.w3cplus.com/sites/default/files/blogs/2017/1707/figure-18.png)

摘自：[深入PostCSS Web设计](http://www.w3cplus.com/preprocessor/postcss-book.html)

![PostCSS处理流程图](http://mmbiz.qpic.cn/mmbiz_png/4biceW6butxr866PuARubjx3hWNgvic4RGOiar4f2AS0zt2Er1vUC7pf5Ey0rMfJmJVE6sWhFJjMqrYbBy9yc4cJw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

摘自：[写CSS的姿势](https://mp.weixin.qq.com/s/2CgHH0gwyUNkLcGgmICtfw)

这两张图则应该是说明了我之前的假设，插件中的执行顺序**自上而下**。

但是这些资料不明不白的，我决定进一步阅读相关文章找出真相，不过研究过程中，我也依然深思这样一个问题，也许我本不应该纠结这个，因为或许根本没有意义？或者这个工作方式并非与PostCSS本身有关？

于是我又把之前收集的PostCSS的文章翻了一遍，我们再来一个个过一遍😜。

### 先来看看一片来自smashingmagazine的这段[POSTCSS PLUGIN INSTALLATION EXAMPLE](https://www.smashingmagazine.com/2015/12/introduction-to-postcss/#postcss-plugin-installation-example)

> In the array, we can include the necessary `require` statements that return the plugins and that are then called immediately. If you would like to read more about this concept, look through “[Functions as First-Class Citizens in JavaScript](http://ryanchristiani.com/functions-as-first-class-citizens-in-javascript/)” by Ryan Christiani.

会不会跟这个有关呢，我先埋个伏笔。

### 再来看看另一篇[PostCSS Quickstart Guide: Exploring Pluginsd](https://webdesign.tutsplus.com/tutorials/postcss-quickstart-guide-exploring-plugins--cms-24566)的**Plugin Execution Sequence**中这段

> One of the main considerations you have to make when loading up the array of PostCSS plugins is the order in which you run them. You’ll have to pause and think through your list, determining if one plugin might need to run after another in order to do what you want it to.

鄙人译：*有一个非常重要的需要考虑的一点是，当你在加载PostCSS插件数组中顺序就是你执行他们的顺序。因此你有必要好好在这个插件列表这下功夫思考一下，来确定你想要的一个接一个的插件执行顺序。*

原文第一句话很复杂，所以英语不好的理解起来可能有些费劲，姑且可以分成以下几段分别翻译再合并<u>One of the main considerations you have to make</u> when <u>loading up the array of PostCSS plugins</u> is <u>the order in</u> which <u>you run them</u>.

然后这段原文后也有示例，大家可以自己看看加深理解和认识。并且也有一个小结论：

> The load order for plugins is something that will change with each set of plugins, so you may find you just need to do a little experimentation sometimes to get everything working together nicely.

鄙人译：你所设置的每个插件都会被加载的插件顺序所影响，因此强烈建议你在某些情况下多做些测试来让你的插件们运行的更加完美~

好了看到这里，其实答案大致已经揭晓，也许你觉得这不是很自然的从上而下么，为何作者要纠结这么久还写了这么一篇长篇大论的文章。如果你还没看过之前我为何纠结这个顺序问题，可以看看[PostCSS自学笔记（二）【插件篇】#CSSNANO](https://github.com/whidy/Notes/blob/master/notes/201708/PostCSS%E8%87%AA%E5%AD%A6%E7%AC%94%E8%AE%B0%EF%BC%88%E4%BA%8C%EF%BC%89%E3%80%90%E6%8F%92%E4%BB%B6%E7%AF%87%E3%80%91.MD#cssnano)部分以及[PostCSS自学笔记（二）【番外篇一】](https://github.com/whidy/Notes/blob/master/notes/201708/PostCSS%E8%87%AA%E5%AD%A6%E7%AC%94%E8%AE%B0%EF%BC%88%E4%BA%8C%EF%BC%89%E3%80%90%E7%95%AA%E5%A4%96%E7%AF%87%E4%B8%80%E3%80%91.MD)。

### 结论

其实，关于顺序的疑问我觉得差不多就此打住，也许其中的确有些奇怪的现象，但是这个基本不影响或者说没有追究其根本的意义，或许真要打破沙锅问到底就要发邮件给PostCSS作者了。而作为普通开发人员，也许没有必要花太多精力去研究这个，我们知道PostCSS插件的顺序一般来说是从上往下执行的，不要犯低级的顺序错误（例如import写在列表末尾），大部分场景都会得到我们想要的结果了🙃