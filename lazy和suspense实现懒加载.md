## 组件懒加载

* [代码分割资源1](https://www.jianshu.com/p/164be62145a7)
* [代码分割资源2](https://www.jianshu.com/p/61d6920c9e8f)
* [代码分割资源3](https://www.cnblogs.com/MrZhujl/p/12463419.html)

1. 使用原因：

   我们通过webpack可以将项目多个 JavaScript 文件最终打包成为一个bundle文件。加载一个js文件速度要快于加载多个JavaScript文件。不过随着bundle的体积不断增大，最终造成首次加载时间过长，还有就是加载一些不必要的javascript文件。所以我们想是否可以对bundle文件进行拆分来进行按需加载，就此需求代码拆分，也就是 code splitting。

2. react中组件懒加载的方式：

   **机制**：组件预设了懒加载模式，所以同步渲染到该懒加载的组件时需要向服务器请求下载，此时react会抛出异常，同时文件下载也会抛出promise，于是异常和promise一直向上传递，直到传递到suspense，捕获异常与promise，然后等待promise resolve，渲染组件。
   
   ```javascript
   const Foo = React.lazy(() => import('../components/Foo'));
   
   export default class LazyPage extends Component {
       render() {
           return (
               <div>
                   <Suspense fallback={<div>loading...</div>}>
                       <Foo/>
                   </Suspense>
               </div>
           )
       }
}
   ```

3. suspense的进阶用法：fetch

   [suspense做fetch资源1](https://segmentfault.com/a/1190000017483690)

   [suspense做fetch资源2](https://blog.csdn.net/wangweiren_get/article/details/86624820)

   
   
   原理上依然是通过suspense捕获promise，等待resolve再执行render
   



```javascript
import React, { Suspense } from "react";
import ReactDOM from "react-dom";

import "./styles.css";
import { fetchProfileData } from "./fakeApi";

const resource = fetchProfileData();

function ProfilePage() {
  return (
    <Suspense
      fallback={<h1>Loading profile...</h1>}
    >
      <ProfileDetails />
      <Suspense
        fallback={<h1>Loading posts...</h1>}
      >
        <ProfileTimeline />
      </Suspense>
    </Suspense>
  );
}

function ProfileDetails() {
  // Try to read user info, although it might not have loaded yet
  const user = resource.user.read();
  return <h1>{user.name}</h1>;
}

function ProfileTimeline() {
  // Try to read posts, although they might not have loaded yet
  const posts = resource.posts.read();
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.text}</li>
      ))}
    </ul>
  );
}

const rootElement = document.getElementById(
  "root"
);
ReactDOM.createRoot(rootElement).render(
  <ProfilePage />
);
```

