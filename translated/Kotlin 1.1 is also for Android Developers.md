---
title: "[译]Kotlin 1.1 is also for Android Developers"
date: 2017-04-05 16:13:00
author: Roman Belov
tags:
keywords:
categories: 官方动态
reward: false
reward_title: Have a nice Kotlin!
reward_wechat:
reward_alipay:
source_url: https://blog.jetbrains.com/kotlin/2017/04/kotlin-1-1-is-also-for-android-developers/
---

我们对 Kotlin 1.1 的发布感到非常兴奋。此版本包含的新特性对 Java 开发人员非常有用，并给 JVM 开发带来了更多新的可能性。
但这些新功能，如 [协程](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md) ， 或者 [类型别名](https://github.com/Kotlin/KEEP/blob/master/proposals/type-aliases.md) （举几个例子），对 Android 开发人员来说就像科幻小说。
我们仍然停留在古老的 Java 6 中，几乎没有什么改进，迫使我们以其他平台中大多数开发人员几乎被遗忘的方式开发。
所以一个理所当然的问题是：Kotlin 团队能够保持与 Java 6 的兼容性，同时保留所有这些新功能吗？答案是：当然！
<strong>所有新的特性仍然可用于Java 6，当然也可以在 Android 上使用。</ strong>下面我来展示其中的几种特性的用法，看看这些新特性如何简化 Android 开发。

{% raw %}
<p><span id="more-4826"></span></p>
{% endraw %}

# 类型别名：提高监听器代码的可读性

当然，类别别名有很多不同的应用场景。但是，我想到的第一个就是提高监听器的可读性，同时保持使用 lambdas 的能力。

如果你还没听说过 [类型别名](https://github.com/Kotlin/KEEP/issues/4) ，它们基本上就是一种将复杂类型重命名为一个可读性更高的类型的方法。

例如，你可以有一个 <code> RecyclerViewadapter </code>，它会收到一个监听器。你可能知道，<code> RecyclerView </code>没有一个标准的方式来处理点击，就像 <code> ListView </code> 一样，所以我们必须自己创建一个。

让我们想象一下，我们希望有一个可以访问视图的监听器。我们的适配器类可能如下所示：

{% raw %}
<p></p>
{% endraw %}

```kotlin
class MyAdapter(val items: List<Item>, val listener: (View) -> Unit) : RecyclerView.Adapter<MyAdapter.ViewHolder>() {
    ...
}
 
```

{% raw %}
<p></p>
{% endraw %}

而<code> ViewHolder </code>需要接收该监听器，才能将其分配给视图的点击监听器：

{% raw %}
<p></p>
{% endraw %}

```kotlin
class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
    fun bind(item: Item, listener: (View) -> Unit) {
        itemView.setOnClickListener(listener)
    }
}
 
```

{% raw %}
<p></p>
{% endraw %}

这不是一个非常复杂的情况，但是正如你所看到的，我们需要重复一下 lambda 定义，这种定义在没有上下文时可能会导致阅读困难。
但是，我们可以创建一个代表点击监听器的类型别名：<br/>
<code> typealias ClickListener =（View） - ＆gt;单位</code> <br/>

然后在我们需要的那个监听器的每一个地方使用它：<br/>
<code> class MyAdapter（val items：List＆lt; Item＆gt，val listener：ClickListener）</code> <br/>

或者
<code> fun bind（item：Item，listener：ClickListener）{...} </code>
# 数据类现在更强大了

数据类是伟大的，它避免了大量的模板代码。但是他们缺乏一些能力，这使得它们在某些情况下不可用。
Kotlin 1.1 中的新内容之一是继承：数据类现在可以继承其他类。

这允许数据类成为密封类的一部分：

{% raw %}
<p></p>
{% endraw %}

```kotlin
sealed class UiOp {
    object Show : UiOp()
    object Hide : UiOp()
    data class Translate(val axis: Axis, val amount: Int): UiOp()
}
 
```

{% raw %}
<p></p>
{% endraw %}

<strong>因为现在密封类可以在父类外面定义</strong>，所以我们也可以这样写：

{% raw %}
<p></p>
{% endraw %}

```kotlin
sealed class UiOp
object Show : UiOp()
object Hide : UiOp()
data class Translate(val axis: Axis, val amount: Int)
 
```

{% raw %}
<p></p>
{% endraw %}

# 解构 lambdas

自从第一个版本开始，因为生成了<code> componentX（）</code>方法，数据类是可以被解构的。您可以将数据类的内容赋值给多个变量，如下所示：

{% raw %}
<p></p>
{% endraw %}

```kotlin
data class Item(val text: String, val url: String)
 
val (text, url) = item
 
```

{% raw %}
<p></p>
{% endraw %}

但是还少了一个非常强大的特性：能够在 lambdas 上解构。但是等待结束了！你现在可以这样写：

{% raw %}
<p></p>
{% endraw %}

```kotlin
fun bind(item: Item) = item.let { (text, url) ->
    textView.text = text
    imageView.loadUrl(url)
}
 
```

{% raw %}
<p></p>
{% endraw %}

这对 map 中的 pairs 或 key/value sets 也是非常有用的。
# 本地委托属性

委托属性在为类的属性提供额外能力方面的作用是众所周知的。

其中最有用的一个是<strong>懒惰委托</strong>，它会延迟赋值操作，直到该属性第一次使用为止。

延迟加载对变量也是非常有用的，但是 Kotlin 缺乏这个特性。

现在，通过本地委托的属性，我们可以做到了：

{% raw %}
<p></p>
{% endraw %}

```kotlin
fun testLocalDelegation() {
    val database by lazy { createDatabase() }
    val cache by lazy { createMemoryCache() }
 
    if (mustUseDatabase()) {
        database.use { ... }
    } else {
        cache.use { ... }
    }
}
 
```

{% raw %}
<p></p>
{% endraw %}

虽然这个例子可以在没有使用懒惰委托的情况下解决，但它有助于理解这个概念。

例如我们有几个大的对象可能会被使用，也可能不会被使用。通过使用懒惰，我们可以延迟实例化，直到我们确定我们要使用它再去实例化。

第一次调用时，大括号内的代码被执行，执行结果将被缓存，以备稍后再次使用。
# 再也不用在 lambdas 上声明从未使用的变量了

在 lambdas 中声明从未使用的参数变量是非常常见。

这是因为在 Kotlin 1.0 中，我们没有办法丢弃未使用的参数。

例如，在本文中，我介绍了如何使用委托更新 RecyclerView 适配器，最后我使用了以下代码：

{% raw %}
<p></p>
{% endraw %}

```kotlin
var items: List<Content> by Delegates.observable(emptyList()) {
    prop, old, new ->
    autoNotify(old, new) { o, n -> o.id == n.id }
}
 
```

{% raw %}
<p></p>
{% endraw %}

代码中的 prop 参数从未被使用过，但是之前我们还是必须声明。现在开始，我们可以使用下划线来避免这种情况：

{% raw %}
<p></p>
{% endraw %}

```kotlin
var items: List<Content> by Delegates.observable(emptyList()) {
    _, old, new ->
    autoNotify(old, new) { o, n -> o.id == n.id }
}
 
```

{% raw %}
<p></p>
{% endraw %}

如果你一个参数也没有用的话更差劲。因为即使不使用这些参数，也需要声明 lambda 的所有参数。

但是现在开始我们可以忽略这些参数了：

{% raw %}
<p></p>
{% endraw %}

```kotlin
var items: List<Item> by Delegates.observable(emptyList()) {
    _, _, _ ->
    notifyDataSetChanged()
}
 
```

{% raw %}
<p></p>
{% endraw %}

这样需要定义的变量少了，代码也变得更易读。现在你一眼就能看出来哪些参数是有用的了。
# 协程

加入协程特性是 Kotlin 1.1 中最令人兴奋的消息。尽管这个版本的特性还是“实验性”的，<strong> 但它们功能齐全，你可以在今天开始在项目中使用它们</strong>。

协程将让您以同步方式编写异步代码，允许在某些时候暂停执行，并等待结果，同时编写顺序代码。

你可能已经知道， Kotlin 中的协程不是一个库或具体的实现，<strong> 而是一种允许通过它创建库的语言特性</strong>。

因此，尽管结果代码看起来可能相似，但重要的是要知道创建这些辅助线程并返回主线程的“齿轮”是什么，这在Android中非常重要。

幸运的是，Kotlin社区移动速度很快，已经有几个库将协程的特性带入<strong> Android </strong>。这里有一些例子：

你第一个想看的可能是 Jetbrains 官方提供的几个例子：

* [kotlinx-coroutines-android](https://github.com/Kotlin/kotlinx.coroutines/tree/master/ui/kotlinx-coroutines-android) ，该库提供了一个可以在 Android 上使用的协程实现。
* [Anko](https://github.com/Kotlin/anko)，在最新的 beta 版中，框架内部的许多监听器都已经支持了协程。

还有许多其他第三方库实现自己的协程：

* [由 Niek Haarman 实现的 AsyncAwait-Android](https://github.com/nhaarman/AsyncAwait-Android)
* [由 Metalab 实现的 Async / Await]("https://github.com/nhaarman/AsyncAwait-Android")
* 如果你只是想在 Retrofit 中使用协程，可以参考下 [Andrey Mischenko 实现的 kotlin-coroutines-retrofit](https://github.com/gildor/kotlin-coroutines-retrofit)。

强烈建议你不仅要使用它们，还要去了解它们是如何实现的。这就是开源的魔力。
# 其他关于 Android 开发炫酷特性

这个版本还有非常多的改进，但是我想强调一些更侧重于 Android 开发的。

第一个是现在可以通过使用以下命令来启用 Jack 编译器的支持：<code> jackOptions {true} </code>。 Google 已宣布他们会废弃 Jack 工具链，但是直到 Android Studio 2.4 的最终版本发布前，你在 Java 8 上还是可以这么用的。

另外，<strong>一个新的计划将使用</strong> <code> @JvmOverloads </code> <strong>来实现自定义视图的构造函数</strong>，实际上，这将允许我们使用构造函数和参数的默认值，仅用一行（是的，很长的一行）代码实现自定义视图的构造函数。

{% raw %}
<p></p>
{% endraw %}

```kotlin
class CustomView @JvmOverloads constructor(
        context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {
    ...
}
 
```

{% raw %}
<p></p>
{% endraw %}

# 结论

Kotlin 1.1 版本带来了一大批新的特性，使得我们不可避免的问自己为什么还要继续使用 Java。

毫无疑问，Kotlin 为 Android 开发人员带来了强大的能力，从今天开始你就可以用 Kotlin 编写你的 Android 应用。

如果您想一边开发应用程序，一边从头开始学习 Kotlin for Android，那么你可能会对<strong> <a href="https://antonioleiva.com/kotlin-android-developers-book/"> Kotlin for Android Developers </a>这本书感兴趣</strong>。
