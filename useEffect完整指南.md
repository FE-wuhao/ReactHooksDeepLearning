## dan的博客学习useEffect 2020/5/27 21:25

1. ***useEffect、state、props***对于每一次的渲染来说都是独立的，所以在一次***effect***中***setstate***将不会得到更新的***state***
2. `setstate()`可以设置函数作为参数，且函数自身的参数是***state***，也就是说，`setstate((x)=>{ return x+1})`可以在不引用state的情况下做到state的自增
3. ***useEffect***中使用到的值必须要设置依赖，这是一种使用原则，如果会导致问题也要保证在不违背原则的前提下解决问题
4. ***useReducer***会在每次组件刷新重新生成新的***reducer***吗？***dispatch***是不变的，与之对应的***action***描述似乎也是不变的，但是当描述传递到**reducer**中去时，***reducer***是新的，这种情况下如果依赖了***props***那么***props***也是新的，那么就能监听到***props***的更新从而影响到***state***的更新。有待验证.
5. 一个组件文件(组件名.tsx)中可以包含不止一个**fucntion**，所以对于不依赖于组件内部任何值的函数可以提取到组件函数外面，需要的时候直接调用即可。
6. 论**useMemo**和**useCallback**各自的作用：

   * **useMemo**通常用于缓存函数的返回值，在依赖未发生变化的情况下不重新执行函数而直接返回之前函数执行的结果
   * **useCallback**通常用于保证从父组件传入子组件的函数，在依赖未发生变化的情况下，传入相同的函数到子组件，从而避免子组件不必要的重新渲染。
3. **useCallback**的使用场景：

   * 父组件向子组件传递的函数，通过**useCallback**缓存
   * 一个组件如果有**useEffect**的依赖是组件内的函数，那么就不希望每次重新渲染后都生成一个新的函数，从而导致**useEffect**的非预期执行增加开销。也可以通过**useCallback**添加一个依赖，判断渲染前后依赖是否发生变化，没有变化就不创建新函数，从而减少性能浪费。
   * [**useMemo与useCallback使用指南**](https://blog.csdn.net/sinat_17775997/article/details/94453167)

4. **useState**、**useEffect**、**useMemo**、**useCallback**都用到了闭包，所以他们访问到的都只是当前渲染帧的数据，因为他们在每一个新的渲染帧中都会创建新的自己，想要追溯之前渲染帧中的数据，需要事先用**useRef**保存好，并且不再去改变它，这样在你想要的任意时刻都可以获取到过去的某一帧的值。

5. 函数的副作用：所谓的副作用，就是除了返回一个值外还对主函数产生了其他影响，比如说改变了主函数中定义的其他变量的值，因此就有了**纯函数**和**非纯函数**的说法了，所谓的函数式编程也就是不允许产生副作用，也就是无副作用编程。useEffect可以处理副作用，但是**useCallback**和**useMeomo**不可以。

   [**函数的副作用**](http://www.fly63.com/article/detial/1176)

6. 竞态：当一个**async**过程中所依赖的请求参数发生变化会再次出发**async**，由于异步请求结束的先后顺序是不确定的，这就产生了竞态，先发出的async的结果可能会比后发出的回来的晚，然后就会覆盖正确的结果。

   * 解决方案：与杰哥的思路一致，在**useEffect**中创建一个布尔值初值为**false**，在**useEffect**的**return**函数中设置该布尔值为**true**，标记当前渲染帧已经结束，进入下一个渲染帧发出下一个**async**，那么当布尔值变成**true**这件事发生在渲染帧结束之前，就直接阻止后续对该请求结果的使用。

     ```javascript
     function Article({ id }) {
       const [article, setArticle] = useState(null);
     
       useEffect(() => {
         let didCancel = false;
     
         async function fetchData() {
           const article = await API.fetchArticle(id);
           if (!didCancel) {
             setArticle(article);
           }
         }
     
         fetchData();
     
         return () => {
           didCancel = true;
         };
       }, [id]);
     
       // ...
     }
     ```

7. useEffect其实并不应该用于数据请求，因为他与渲染是同步的，将来suspense也许会成为主力军，让渲染等待异步请求数据回来以后再进行。
