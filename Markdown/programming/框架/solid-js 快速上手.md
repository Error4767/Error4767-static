## Solid-js
>用于构建用户界面的声明式、高效且灵活的 JavaScript 库  

Solid 使用了和 React 相似的语法和类似 Svelte 的预编译  
Solid 使用上类似于 React，使用 JSX 语法，但不同于 React， 组件只会初始化一次，并不是 state 改变就重新运行渲染整个组件，这类似于 Vue3 的 setup

### 为什么选择 Solid
[Solid 官网](https://www.solidjs.com/) 给出了以下理由
* 高性能 - 始终在公认的 UI 速度和内存利用率基准测试中名列前茅
* 强大 - 可组合的反应式原语与 JSX 的灵活性相结合
* 务实 - 合理且量身定制的 API 使开发变得有趣而简单
* 生产力 - 人体工程学和熟悉程度使构建简单或复杂的东西变得轻而易举  

### 主要优势
高性能 - 接近原生的性能，在 [js-framework-benchmark](https://krausest.github.io/js-framework-benchmark/index.html) 排名中名列前茅  
极小的打包体积 - 编译为直接的DOM操作，无虚拟DOM，极小的运行时（类似于 Svelte），适合打为独立的 webComponent 在其它应用中嵌入  
易于使用 - 近似 React 的使用体验，便于快速上手

### 快速开始
#### 新建项目
````
npx degit solidjs/templates/js my-app
cd my-app
npm i
npm run dev
````
#### 基本示例
这里将 App 组件渲染到 body 容器中  
>这里修改默认示例， 从零开始尝试
````javascript
// App.JSX
import { render } from "solid-js/web";

function App() {
  return (
    <div>Solid My App</div>
  );
}
// 组件声明也可以直接用箭头函数
/*
const App = ()=> (<div>Solid My App</div>);
*/


render(() => <App />, document.querySelector("body"));
````

是不是看起来非常熟悉，就和 React 一样，非常舒服

#### 导入组件与使用以及 props
与 React 类似的使用方法, 但不能解构 props，否则将失去反应性  
````javascript
// App.JSX
import { render } from "solid-js/web";

import Component1 from "./Component1.jsx";

function App() {
  return (
    <div>
      Solid My App
      <Component1 text={"component1"}></Component1>
    </div>
  );
}

render(() => <App />, document.querySelector("body"));

// Component1.jsx
export default function Component1(props) {
  return (
    <div>{props.text}</div>
  )
}
````

#### 基本的反应性
##### createSignal
signal 是 Solid 中最基本的反应性单元，此函数类似于 React 的 useState，但返回函数用于获取调用它获取值，而不是像 React 一样直接取得值，下列是一个基本的 Counter 示例
````javascript
import { createSignal } from "solid-js";

export default function Counter() {
  const [count, setCount] = createSignal(0);
  return (
    <button onClick={()=> setCount(count() + 1)}>
      {count()}
    </button>
  )
}
````

##### createMemo
createMemo 用于生成只读的派生值，类似于 Vue 中的 computed，与上面的相同，也需要通过调用来获取值  

````javascript
import { createSignal, createMemo } from "solid-js";

export default function Counter() {
  const [count, setCount] = createSignal(0);
  // count 的平方派生自 count，在依赖改变的时候自动更新
  const countPow2 = createMemo(()=> count() ** 2);
  return (
    <button onClick={()=> setCount(count() + 1)}>
      {count()} | {countPow2()}
    </button>
  )
}
````

##### createEffect
createEffect 一般用于副作用，在状态改变的时候运行副作用  
它类似于 React 中的 useEffect 但其自动收集依赖，无需显式声明依赖，这和 Vue 中的 watchEffect 作用相同  
````javascript
import { createSignal, createEffect } from "solid-js";

export default function Counter() {
  const [count, setCount] = createSignal(0);
  // 每当依赖改变就会重新运行该副作用
  createEffect(()=> console.log(count()));
  return (
    <button onClick={()=> setCount(count() + 1)}>
      {count()}
    </button>
  )
}
````
如果需要显式声明依赖，参考 [Solid createEffect 显式声明依赖](https://www.solidjs.com/tutorial/reactivity_on)

#### 样式

先创建一个样式文件以便下面使用
````css
/* main.css */
.container {
  width: 100px;
  height: 100px;
  background-color: green;
}
.text {
  font-size: 20px;
  color: red;
}
````

##### 基本使用
样式使用也与 React 非常类似，只是使用 class 而不是 className
````javascript
import style from "./main.css";

export default function Container() {
  return (
    <div class={style.container}>
      <span class={style.text}>text</span>
    </div>
  )
}
````
##### classList
用于设置给定的 class 是否存在, 也可以绑定响应式  
下列是一个点击切换 class 的示例
````javascript
import style from "./main.css";
import { createSignal } from "solid-js";

export default function Container() {
  const [hasTextClassName, setHasTextClassName] = createSignal(false);
  return (
    <div classList={
      {
        [style.container]: true,
        [style.text]: hasTextClassName()
      }
    } 
    onClick={
      ()=> setHasTextClassName(!hasTextClassName())
    }>
    text
    </div>
  )
}
````

#### ref
ref 用于获取 DOM 节点本身
````javascript
export default function Container() {
  let $container;
  return (
    <div ref={$container}>
      container
    </div>
  )
}
````

#### 基本的控制流
>控制流大多可以用 JSX 实现相同功能，但是使用其则具有高于 JSX 的性能，Solid 可以对其进行更多优化  
fallback 是在失败后的显示

##### For
简单的引用键控循环控制流程。

````javascript
export default function Container() {
  return (
    <div>
      <For 
      each={[1,2,3,4,5,6,7,8,9,10]} 
      fallback={<div>Failed</div>}
      >
        {(item) => <div>{item}</div>}
      </For>
    </div>
  )
}
````

##### Show
Show 控制流用于有条件地渲染视图的一部分。它类似于三元运算符 (a ? b : c)，但非常适合模板化 JSX。
````javascript
import { createSignal } from "solid-js";

export default function Container() {
  const [count, setCount] = createSignal(10);
  return (
    <div>
      {/* 在 count 大于 5 的时候渲染*/}
      <Show 
        when={count() > 5} 
        fallback={<div>Failed</div>}
      >
        <div>content</div>
      </Show>
    </div>
  )
}
````

##### Switch
Switch 在有 2 个以上的互斥条件时很有用。可以用来做一些简单的路由之类的事情。  
````javascript
import { createSignal } from "solid-js";

export default function Container() {
  const [count, setCount] = createSignal(10);
  return (
    <div>
      <Switch fallback={<div>Failed</div>}>
        <Match when={count() > 5}>
          <div>count > 5</div>
        </Match>
        <Match when={count() < 5}>
          <div>count < 5</div>
        </Match>
      </Switch>
    </div>
  )
}
````

##### Index
非索引迭代循环控制流程，如果要迭代的不是数组，而是类似对象这类，使用 Index

````javascript
export default function Container() {
  return (
    <div>
      <Index 
      each={{
        name: "name",
        gender: "male",
        age: 100,
        address: "address",
      }} 
      fallback={<div>Failed</div>}
      >
        {(item) => <div>{item}</div>}
      </Index>
    </div>
  )
}
````

##### ErrorBoundary 
错误边界
````javascript

function ErrorComponent() {
  // 抛出错误
  throw new Error("component error");
  return (
    <div>content</div>
  )
}

export default function Container() {
  return (
    <ErrorBoundary fallback={<div>Failed</div>}>
      <ErrorComponent></ErrorComponent>
    </ErrorBoundary>
  )
}
````

##### Portal
和 React Portal 作用相同  
用于将元素渲染到组件之外的地方，这对于模态窗，信息提示等是刚需  
示例：将元素直接渲染到 body 下
````javascript
export default function Container() {
  return (
    <Portal mount={document.querySelector("body")}>
      <div>content</div>
    </Portal>
  )
}
````

##### 其他控制流
参考 [API 文档](https://www.solidjs.com/docs/latest/api#control-flow)