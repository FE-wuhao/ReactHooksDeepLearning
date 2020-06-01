##  setState的秘密与前端的依赖注入   2020/5/29

##### setstate

1. **react**包是独立于目标平台存在的，也就是说不管在那个平台上，组件都是以同样的方式导入导出。与之相对应的，不同渲染器则是对应各个不同的平台，比如说**ReactDOM**对应的就是**dom**，也就是说**web**，所谓的**react**的引擎也就在各个渲染器内部。**react**包是用来暴露**api**的，而**api**的真正实现则是渲染器。
2. **setState**的真正秘密就是通过渲染器创建组件实例返回给**React.component**，实例上绑定了一个updater，组件调用**setState**的时候**React.component**再将参数转发给updater，从而让渲染器执行，**React.component**就是个二传手，真滴捞
3. **hooks**也是一样，依赖于渲染器，他的实例绑定的不是updater，是dispatcher。

##### 前端依赖注入:

体现：

* 模块初始化

