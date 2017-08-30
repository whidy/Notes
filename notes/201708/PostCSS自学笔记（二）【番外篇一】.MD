# PostCSS自学笔记（二）【番外篇一】

上一期有提到结合[postcss-px2rem](https://github.com/songsiqi/px2rem-postcss)插件来处理移动端适配的方案，以及相关的避坑方法，之后总觉得这个解决方案问题太多，也就诞生了另一套方案**运用[postcss-pxtorem](https://github.com/cuth/postcss-pxtorem)插件来进行处理**。

**那么这期番外篇讲的就是postcss-px2rem和postcss-pxtorem的利弊了。**

题外话，其实我也很好奇，为什么postcss-px2rem没有被收录在PostCSS[插件列表](https://github.com/postcss/postcss/blob/master/docs/plugins.md)中，难道是因为收录者认为postcss-pxtorem可以取代它？不过两者各有利弊，就看开发者的喜好了。

因为之前关于postcss-px2rem使用方法和可能存在的问题说的太多了，这次主要介绍postcss-pxtorem（截至2017年8月30日，我试过查找关于该插件的中文资料，完全没有）的基本功能和个人使用一些感受。

首先，我们用这个插件为了就是要rem统治世界。然而，有些场景并不合适，比如1px边框问题，我们希望并不转换这个边框。（以前都说文字大小不建议转换rem，我实在找不到有力证据在当前前端开发环境下保留字体大小的px值，因此此处不做处理）

**这两个插件处理忽略某些样式的转换方式是怎样的呢？**

postcss-px2rem通过注释解决。

postcss-pxtorem通过配置规则和单位使用`Px`或`PX`来解决。

**另外，这两个插件各有什么优势呢？**

postcss-px2rem可以生成根据dpr值不同的多个选择器，官方主要用它来做字体大小的适配。以及多个版本的css文件。

postcss-pxtorem则有强大的转换规则来适应不同的场景，下文将进行详细介绍。

因此个人现在倾向于使用postcss-pxtorem，至于是否有坑，还有待考究。接下来，开始介绍以下postcss-pxtorem的使用方法。

采用[postcss-pxtorem](https://github.com/cuth/postcss-pxtorem)避免了postcss-nested注释问题,具体配置大致如下

```javascript
require('postcss-pxtorem')({
  rootValue: 75,
  unitPrecision: 5,
  propList: ['*'],
  selectorBlackList: [],
  replace: true,
  mediaQuery: false,
  minPixelValue: 12
})
```

假设设计稿750宽，这里设置简单说明一下（没说的是我还没弄明白或者是不重要的😂）：

- rootValue为75，说是对根元素大小进行设置。可能类似[px2rem](https://www.npmjs.com/package/px2rem)中的remUnit参数吧

- unitPrecision为5，起初我真不知道这个官方说的*The decimal numbers to allow the REM units to grow to.*是啥意思，搞了半天才观察出来，原来是**转换成rem后保留的小数点位数**。。。

- propList是一个存储哪些将被转换的属性列表，这里设置为`['*']`全部，假设需要仅对边框进行设置，可以写`['*', '!border*']`意思是排除带有border的属性，当然这里会有一个问题，也许有时候不想对border其他样式处理例如`border-radius`所以也不是很好。

- selectorBlackList则是一个对css选择器进行过滤的数组，比如你设置为`['fs']`，那例如`fs-xl`类名，里面有关px的样式将不被转换，这里也支持正则写法。

- minPixelValue是一个非常不错的选项，我设置了12，意思是所有小于12px的样式都不被转换，那么border之类的属性自然会保留px值了。而刚才提到的border-radius如果为了创造圆形等特殊较大圆弧时则还是会转换成rem，来配合对应的width和height（当然，你也可以用继承width或者height的变量来设置radius）。

  > 需要注意的是，以下情况并不会保留为px！
  >
  > ```css
  > .test-radius {
  >   width:20px;
  >   height:20px;
  >   border-radius: calc(@width / 2);
  >   background-color:#ccc;
  > }
  > ```
  >
  > 根据反复测试，calc运算是来自cssnano插件，然而[cssnano](http://cssnano.co/)有必要放在最后执行，所以无法满足计算后的10px在进行pxtorem转换，不过这种情况也是比较合理的。假设width和height转换为rem，而圆角是px，个人感觉不可避免的会造成圆形错误的情况（是否有可能改圆角px值实际上永远大于转换后的rem的50%？有待考究！），所以这种情况暂时就不考虑了，让其单位均保持一致即可。

  写到这里我又陷入了沉思，因为有个问题不明白了。根据postcss.config.js配置cssnano是在最后面，pxtorem是在其前面，那么如何做到对此段样式转换的顺序。

  这段代码应该先是postcss-property-lookup对@width进行处理，然后进行calc(@width / 2)计算，最后对px检测转换，再进行cssnano压缩。而实际上有点诡异。**难道postcss.config.js中插件的执行顺序并非单纯的从上而下！**希望不久的将来这个疑问将被解决，或者我也怀疑postcss官方文档实际有指出，只是个人英文能力较差被我忽略掉了😥。

  另一方面，关于此段CSS在画圆上有一些需要注意的，其实这里如果写圆用50%即可，我发现某些情况下（可能是圆形很小）如果按照除以2的写法转换成rem似乎不圆，所以在现代开发来看移动端画圆就50%了！所以上例仅做测试好了~

  额外阅读，关于[border-radius](https://caniuse.com/#search=border-radius)的一些事项。

  对了忘了说了，css样式代码中将px写成`Px`或者`PX`他也不会转换成rem的~

最后附上我实际应用场景的[demo](https://github.com/whidy/mobileweb/tree/pxtorem%E6%96%B9%E6%A1%88)，仅供大家测试使用。