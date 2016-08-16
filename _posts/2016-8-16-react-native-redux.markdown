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

> 当ReactNative项目变大时state变得不可预测，不可预测的意思是到处修改和使用导致维护与debug很困难，所以就要有个管理state的*稻草*，
> 这个救命*稻草*就是Redux。但注意一点Redux并不仅仅为ReactNative而生。
> 声名：本文更适合有ReactNative基础的朋友。

# Redux
- - -

Redux是为javascript而生的可预测的状态容器，是[Flux](http://facebook.github.io/flux/)的进化。什么是Flux？没用过。为什么是可预测的？下面会解释。

Redux由Action、Reducer、Storage三部分组成，先来看看官方代码（里面有中文的金玉良言，不要一目十行，请慢慢体会）：
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
    
    store.subscribe(() =>
      console.log(store.getState())
    )
    
    // The only way to mutate the internal state is to dispatch an action.
    // The actions can be serialized, logged or stored and later replayed.
    store.dispatch({ type: 'INCREMENT' })
    // 1
    store.dispatch({ type: 'INCREMENT' })
    // 2
    store.dispatch({ type: 'DECREMENT' })
    // 1
```



