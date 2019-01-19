## Flutter 布局的一些思考

这篇文章深入理解了官方提供的三篇关于布局的文章而提炼出来的前端视角，浓缩虽有精华可也有瑕疵，只能着重梳理相关的知识点，对于在实践的过程里该如何布局，有着重要的指导意义；

- [https://flutter.io/docs/development/ui/layout](https://flutter.io/docs/development/ui/layout)
- [https://flutter.io/docs/development/ui/layout/tutorial](https://flutter.io/docs/development/ui/layout/tutorial)
- [https://flutter.io/docs/development/ui/layout/box-constraints](https://flutter.io/docs/development/ui/layout/box-constraints)

让我们先来看一看前端的布局知识点归纳对于 Flutter 布局可参考的地方：

- 盒模型
- div
- position
- flex

稍微不同的是 Flutter 布局的核心机制是 Widget，因为在 Flutter 中布局模型也是 Widget，比如 Text Image 和 Icon，这个设计的思路借鉴了 React 的设计风格，让我们来看一张很直白的图：

![](../images/flutter-23.png)

大多数情况下如上图所料你可以使用 Container 来添加一些填充，边距，使用 Row 和 Column 来进行排列，通过这样的组合才能还原出有效的设计图。那么，对于前端的同学们来说该如何有一个对比的参考？我将它进行了一些归纳。

- Container 可以对比参考 div 
- Row 可以对比参考 flex-direction: row
- Column 可以对比参考 flex-direction: column
- Center 可以对比参考 justify-content: center 和 align-items: center
- Stack 可以对比参考 position
- Padding 可以对比参考 padding

不过，对于 Flutter 来说它提供了几十种布局类 Widget，对于入门新手曲线较高，因为每一个 Widget 的理解和掌握并在实践中合理的选择哪一个布局 Widget 成了问题。不过这样的方式优点也很突出，毕竟这些 Widget 是官方维护的，在优化方向上能有更多的掌控，对于生态中的开发者而言也能开发出性能和体验更好的应用。

我给布局类的 Widget 归纳了三点：

- 只有一个元素 child 的布局 Widget，比如 Container
- 可以 add 多个子元素 children 的布局 Widget，比如 Row
- 类似 layout helper 布局，比较典型的如 ListView

### box constraints

接下来我们还需要聊一聊 box constraints，应该来说它才是 Flutter 布局的核心设计，非常有趣的是这样的设计思路和 iOS 的约束布局，React 中大行其道的 CSS in JavaScript 有了那么点相同的味道。

在 Flutter 里 Widget 的渲染是由 RenderBox 来呈现的，反而它的布局约束依赖于父 Widget 的实现，其自身会在这个父 Widget 中自动调整它的大小高宽。换句话来讲如果你的父 Widget 是 Row ，那么在布局上就是横排列。如果你的父 Widget 是 Container 并且设置了宽度，那么自身 Widget 的渲染也会跟随它父 Widget 的布局约束条件而自行调整。

### 总结

Flutter 布局的过程其实就是拆解组装的过程，在[前言](../preface.md)的布局小节里用pixabay的布局抛砖引玉下。

慢慢学这几十个布局类 Widget 吧 ！