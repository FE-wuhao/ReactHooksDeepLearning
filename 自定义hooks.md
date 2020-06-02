### 自定义hooks

[资料](https://github.com/dt-fe/weekly/blob/v2/080.%E7%B2%BE%E8%AF%BB%E3%80%8A%E6%80%8E%E4%B9%88%E7%94%A8%20React%20Hooks%20%E9%80%A0%E8%BD%AE%E5%AD%90%E3%80%8B.md)

很值得思考的一句话：使用hooks本质上等同于**依赖注入**

1. 本质：

   自定义hooks其实就是一个具有具体功能的函数

2. 重点

   * 自定义hooks是能够根据当前组件生命周期而灵活运行的函数，且复用性强
   * 通过包含useEffect可以轻易地做到在挂载/卸载与所在组件同步

3. 