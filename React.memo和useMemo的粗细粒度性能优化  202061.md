## React.memo 和useMemo的粗细粒度性能优化  2020/6/1

[useMemo与memo性能优化的深入理解](http://www.ptbird.cn/react-hook-useMemo-purerender.html)

##### 粗粒度性能优化：React.memo(组件级)

1. 作用:

   缓存组件级渲染内容，减少重复渲染。

2. 注意点：

   * **memo**只检查**props**的变更所带来的重渲染
   * 诸如**usestate**发生变化所带来的重渲染**memo**并不阻止
   * 在**props**不变的情况下渲染相同内容，**memo**会缓存上次的渲染，不进行多余的重渲染，从而减少重复渲染

3. 进阶：手动比对控制重渲染（类似于SCU）

   ```javascript
   React.memo(arg1,arg2?:Function)
   ```

   接收两个参数,第二个参数为可选参数，为函数类型。第二个函数的返回值可以手动决定是否重新渲染，true表示前后内容一致，无需重新渲染，false则相反。也就是说，如果函数返回固定值true，则永远不会发生重渲染，与SCU类似，手动控制重渲染,不过返回值的作用与SCU相反。

   

4. 用法

   使用React.memo包裹需要控制渲染的组件（一般是子组件），对于只需要针对某特定props变化才进行重渲染的，添加第二参数，进行前后判断，返回值以控制重渲染
   
   ```javascript
   function MyComponent(props) {
     /* render using props */
   }
   function areEqual(prevProps, nextProps) {
     /*
     return true if passing nextProps to render would return
     the same result as passing prevProps to render,
     otherwise return false
     */
   }
   export default React.memo(MyComponent, areEqual);
   ```
   
   

##### 细粒度的性能优化:useMemo（变量级）

1. 作用:

   缓存变量级渲染内容，实现局部pure

2. 注意点

   useMemo是在渲染期间执行的，不能执行[副作用](https://segmentfault.com/q/1010000019475078/a-1020000019477192)

3. 用法

   当需要将某个props经过一段运算，获取其运算结果时，将这段运算的过程包裹到useMemo中，并将用到的props添加到依赖数组中，这样就可以实现计算结果的缓存了，只要依赖的props不变化，那么调用该useMemo返回的结果时永远使用缓存的运算结果
   
   ```javascript
   const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
   ```
   
   

