## 网友整理的博客
https://www.kancloud.cn/hanxuming/vue-iq/728306

## Two-way binding just means that:

When properties in the model get updated, so does the UI.

When UI elements get updated, the changes get propagated back to the model.
## 修饰符
https://cn.vuejs.org/v2/guide/events.html#%E4%BA%8B%E4%BB%B6%E4%BF%AE%E9%A5%B0%E7%AC%A6

#### blur, change
The onBlur event is fired when you have moved away from an object without necessarily having changed its value.


## 在v-for时使用key
用于管理可复用的元素。因为Vue会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。这么做使Vue变得非常快，但是这样也不总是符合实际需求。
加上key后，会重新渲染，之前的值会不存在
## 怎么使用event
https://ustbhuangyi.github.io/vue-analysis/extend/event.html

## 异步更新队列
因为Vue的异步更新队列，$nextTick是用来知道什么时候DOM更新完成的。

Vue在观察到数据变化时并不是直接更新DOM，而是开启一个队列，并缓冲在同一个事件循环中发生的所有数据改变。在缓冲时会去除重复数据，从而避免不必要的计算和DOM操作。然后，在下一个事件循环tick中，Vue刷新队列并执行实际（已去重的）工作。所以如果你用一个for循环来动态改变数据100次，其实它只会应用最后一次改变，如果没有这种机制，DOM就要重绘100次，这固然是一个很大的开销。

理论上，我们应该不用去主动操作DOM，因为Vue的核心思想就是数据驱动DOM，但在很多业务里，我们避免不了会使用一些第三方库，比如 popper.js、swiper等，这些基于原生javascript的库都有创建和更新及销毁的完整生命周期，与Vue配合使用时，就要利用好$nextTick。

## data是函数
data是私有的，组件是可共享的。通过函数return，data不会被共享
https://www.kancloud.cn/hanxuming/vue-iq/776271

## 注册组件
注册组件的本质其实就是建立一个组件构造器的引用，而不是创建了一个组件实例（使用组件才会创建实例）；注册组件不产生新的组件类，但会产生一个可以用来实例化的新方式。


## Package-lock.json
package-lock.json is automatically generated for any operations where npm modifies either the node_modules tree, or package.json. It describes the exact tree that was generated, such that subsequent installs are able to generate identical trees, regardless of intermediate dependency updates.
    Describe a single representation of a dependency tree such that teammates, deployments, and continuous integration are guaranteed to install exactly the same dependencies.

    Provide a facility for users to “time-travel” to previous states of node_modules without having to commit the directory itself.

    To facilitate greater visibility of tree changes through readable source control diffs.

    And optimize the installation process by allowing npm to skip repeated metadata resolutions for previously-installed packages.

#### difference with package.json
“^1.2.12”. This signifies that at a minimum, version 1.2.12 should be used, but any version higher than that is OK, as long as it has the same major version, 1. 

The purpose of the package-lock is to avoid the situation where installing modules from the same package.json results in two different installs.

The idea then becomes that instead of using package.json to resolve and install modules, npm will use the package-lock.json. Because the package-lock specifies a version, location and integrity hash for every module and each of its dependencies, the install it creates will be the same, every single time.

"I wholeheartedly disagree and think you should definitely re-evaluate. Package-lock.json ensures that your entire dependency tree will not change, which can be critical for production environments. Without package-lock you could run into a situation where you run a build twice, without changing ANY of your own code or package.json and end up with two different builds. That lack of consistency can be damning."

https://medium.com/coinmonks/everything-you-wanted-to-know-about-package-lock-json-b81911aa8ab8