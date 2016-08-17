---
layout: post
title: 如何在ReactNative中使用redux
date: 2016.08.16 20:54.000000000 +09:00
category: ReactNative 
tags: react-native, redux
---

# 乱想
- - -
每天想像一件不可能发生的事：用两只脚走路的马撞到了墙

# 碎碎念
![image]({{ site.imageurl }}/assets/images/2016/wang-bao-qiang1.png) <br/>
近日王宝强（本来是想用‘宝宝’来称呼，但是我为人高冷，还是用原名吧）的离婚闹得沸沸扬扬，热度是不是已经远远超过了里约奥运。无下限的网友各种恶搞，从‘潘金莲照顾武大郎’到‘宝宝捉奸’，只说一句：万能的网友捉奸视频有没有？刚看到王宝强的微博，只想说：唉，下贱的小娼妇，但是细想与我何干？毕竟我不知道谁是谁非，这里不做评判。

# 导语
- - -

> ReactNative也出了一年多了，facebook还在忙碌的修复各种问题，截止目前github上还有900个issue未解决。对于我而言对RN还是寄托了很大的期望，一
> 方面是代码高度重用，再就是被javascript灵活性所吸引，希望以后可以不再有iOS和android，那么请叫我RN程序员。

> ReactNative是基于状态的组件化框架，随着RN(ReactNative)项目变大，state变得不可预测，不可预测的意思是state到处修改和使用导致
> 维护与debug很困难，所以就要有个管理state的方式，
> 这种方式就是Redux。但注意一点Redux并不仅仅为ReactNative而生。
> 声名：本文更适合有ReactNative基础的朋友。

> 下面解释一些重要概念，然后解析一个demo。

# Redux
- - -

Redux是为javascript而生的可预测的状态容器，是[Flux](http://facebook.github.io/flux/)的进化。什么是Flux？没用过。为什么是可预测的？下面会解释。

Redux由Action、Reducer、Storage三部分组成，先来看看官方代码（里面有中文的珠玑文字，不要一目十行，请慢慢体会）：

```
    import { createStore } from 'redux'
    
    /*
     *
     *  代码里没定义action，action类似于如下，仅仅是一个普通对象
     *
     *  但是要有**type**，因为reducer（下面马上就会提到）会根据type来更改state
     *
     */
    let action = {
      type: INCREMENT,
      params: xxxx,
    };
    /**
     * This is a reducer, a pure function with (state, action) => state signature.
     * It describes how an action transforms the state into the next state.
     *
     * The shape of the state is up to you: it can be a primitive, an array, an object,
     * or even an Immutable.js data structure. The only important part is that you should
     * not mutate the state object, but return a new object if the state changes.
     *
     * In this example, we use a `switch` statement and strings, but you can use a helper that
     * follows a different convention (such as function maps) if it makes sense for your
     * project.
     */
    /*
     *
     * 只有reducer才能更改state；
     *
     * 返回值为下一个状态，注意下一个state不是对当前状态修改后返回，而是返回了一个全新的state，
     * 也就是说redux中的state是不可改变的，如果想改变状态那就返回一个全新的state
     *   
     */
    function reducer(state = 0, action) {
      switch (action.type) {
      case 'INCREMENT':
        return state + 1
      case 'DECREMENT':
        return state - 1
      default:
        return state
      }
    }
    
    // Create a Redux store holding the state of your app.
    // Its API is { subscribe, dispatch, getState }.
    /*
     * store就用来存放state的容器
     * createStore(reducer) 参数是reducer，把reducer注入到store，这样store最终就成为保存和修改state的容器了
     * 什么是注入？其实就是把reducer赋值给store中的某个变量了，为什么这么做可以google关键字：**设计模式 依赖注入 控制反转**
     */
    let store = createStore(reducer)
    
    // You can use subscribe() to update the UI in response to state changes.
    // Normally you'd use a view binding library (e.g. React Redux) rather than subscribe() directly.
    // However it can also be handy to persist the current state in the localStorage.
    /*
     *
     *  订阅：其参数是一个回调函数，当state改变时就会触发这个回调函数
     *  订阅有个好处（如上英文注释），可以很方便的持久化当前state到localStorage（即将state保存到localStorage）
     *
     */
    store.subscribe(() =>
      console.log(store.getState())
    )
    
    // The only way to mutate the internal state is to dispatch an action.
    // The actions can be serialized, logged or stored and later replayed.
    /*
     *
     * dispatch: 参数为action，store会将此action作为参数执行reducer（上面已经通过createStorage传入到store了）
     *
     */
    store.dispatch({ type: 'INCREMENT' })
    // 1
    store.dispatch({ type: 'INCREMENT' })
    // 2
    store.dispatch({ type: 'DECREMENT' })
    // 1
```
**数据流**动的方向时：action->store->store里的reducer(用来改变state)->更新UI（因为RN是基于状态机的，所以state更改后自动render）<br/><br/>

这也很容理解上面的**可预测**是什么意思，数据流保证了单向流动，store集中管理state，除了reducer没有任何方法修改state，还有重要的一点是state是不可改变的，因为reducer返回的下一个状态state2是基于上一个状态state1的，而不是修改state1然后返回state1.

看完之后有的人酱紫：
![image]({{ site.imageurl }}/assets/images/2016/react-native-redux-scronful1.jpg) <br/>
还有的人酱紫：
![image]({{ site.imageurl }}/assets/images/2016/react-native-redux-maoli.jpeg) <br/>
如果不理解，多看两遍，下面会带着大家一起写个demo。

> 先来欣赏一段扎克伯格的文字
> People often ask me what advice I'd give someone who wants to start their own company.
> My answer is that every good company that I can think of started with someone caring about changing something, not someone deciding > to start a company. Instead of trying to build a company, focus on the change you want to see in the world and just keep pushing 
> forward.
> 有人问我这段字有什么含义，我说：没含义，放松一下😌

# [Redux Thunk][4]
**什么是thunk？** thunk是一个包含了表达式（expression）的函数，用来延迟表达式（expression）的执行。

```
        // 这里1+2是立即执行的
        // calculation of 1 + 2 is immediate
        // x === 3
        let x = 1 + 2;
        
        // 这里1+2不会立即执行，只有在调foo时才会执行
        // calculation of 1 + 2 is delayed
        // foo can be called later to perform the calculation
        // foo is a thunk!
        let foo = () => 1 + 2;
```

现在一些框架的*readme*在写其用处时一般用**Motivation**这个词，动机纯不纯的动机。<br/><br/>
**Redux Thunk middleware**允许你的action creator返回一个function而不是action。Thunk 可以用来延迟dispatch一个action，或者只有满足某个特定条件时才dispatch。inner function把store的dispatch和getState作为参数<br/>
比如：

```
        // action creator 返回一个function去执行异步dispatch
        const INCREMENT_COUNTER = 'INCREMENT_COUNTER';
        
        function increment() {
          return {
            type: INCREMENT_COUNTER
          };
        }
        
        function incrementAsync() {
          return dispatch => {
            setTimeout(() => {
              // Yay! Can invoke sync or async actions with `dispatch`
              dispatch(increment());
            }, 1000);
          };
        }
```

```
        // action creator返回一个function去有条件的执行dispatch
        function incrementIfOdd() {
          return (dispatch, getState) => {
            const { counter } = getState();
        
            if (counter % 2 === 0) {
              return;
            }
        
            dispatch(increment());
          };
        }
```


# 参考

> [1]: http://redux.js.org/ "redux 英文版本"
> [2]: http://cn.redux.js.org/docs/basics/index.html "redux 中文版本"
> [3]: http://stackoverflow.com/questions/29722270/import-modules-from-files-in-directory
 "ES6能否import一个目录（多个文件）"
> [4]: https://github.com/gaearon/redux-thunk "什么是Thunk？"
> [5]: https://github.com/alinz/example-react-native-redux "example-react-native-redux，demo参考了这个项目"
