---
layout: post
title: 如何在ReactNative中使用redux
date: 2016.08.16 20:54.000000000 +09:00
category: ReactNative 
description: ReactNative也出了一年多了，facebook还在忙碌的修复各种问题，截止目前github上还有900个issue未解决。我对RN还是寄托了很大的期望。ReactNative是基于状态的组件化框架，随着RN(ReactNative)项目变大，state变得不可预测，不可预测的意思是state到处修改和使用导致维护与debug很困难，所以就要有个管理state的方式，这种方式就是Redux。但注意一点Redux并不仅仅为ReactNative而生。
tags: react-native;ReactNative;redux
---

<br/>
<br/>

#激发想象力#
用两只脚走路的马撞到了墙，然后...

# 碎碎念
![image]({{ site.imageurl }}/assets/images/2016/wang-bao-qiang1.png) <br/>
近日王宝强（本来是想用‘宝宝’来称呼，但是我为人高冷，还是用原名吧）的离婚闹得沸沸扬扬，热度是不是已经远远超过了里约奥运。无下限的网友各种恶搞，从‘潘金莲照顾武大郎’到‘宝宝捉奸’，只说一句：万能的网友捉奸视频有没有？刚看到王宝强的微博，只想说：唉，下贱的小娼妇，但是细想与我何干？毕竟我不知道谁是谁非，这里不做评判。

# 导语
- - -

> ReactNative也出了一年多了，facebook还在忙碌的修复各种问题，截止目前github上还有900个issue未解决。对于我而言对RN还是寄托了很大的期。
> ReactNative是基于状态的组件化框架，随着RN项目变大，state变得不可预测，不可预测的意思是state到处修改和使用导致
> 维护与debug很困难，所以就要有个管理state的方式，
> 这种方式就是Redux。但注意一点Redux并不仅仅为RN而生。
> Notice：本文更适合有RN基础的朋友，如果你学过reactjs也是可以的。

> 下面解释一些重要概念，然后解析一个[demo][6]。

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
![image]({{ site.imageurl }}/assets/images/2016/react-native-redux-scornful1.jpg) <br/>
还有的人酱紫：
![image]({{ site.imageurl }}/assets/images/2016/react-native-redux-maoli.jpeg) <br/>
如果不理解，多看两遍，下面会带着大家一起写个demo。

> 先来欣赏一段扎克伯格的文字
> People often ask me what advice I'd give someone who wants to start their own company.
> My answer is that every good company that I can think of started with someone caring about changing something, not someone deciding > to start a company. Instead of trying to build a company, focus on the change you want to see in the world and just keep pushing 
> forward.
> 有人问我这段字有什么含义，我说：没含义，放松一下😌

# Redux Thunk

[github地址在这里][4]<br/>
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
现在一些框架的*readme*在写其用处时一般用**Motivation**这个词，动机纯不纯的动机。
<br/>
<br/>
**Redux Thunk middleware**允许你的action creator返回一个function而不是action。<br/>
Thunk 可以用来延迟dispatch一个action，或者只有满足某个特定条件时才dispatch。<br/>
inner function把store的dispatch和getState作为参数<br/>
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

# Demo->Discolor
[进这里，点击右上角的star](https://github.com/CoderGLM/react-native-redux-example/tree/master/Discolor)
实现的功能是，页面上有两个按钮，点击按钮页面背景变色，第一个是变成红色，第二个是变成绿色<br/>
在此demo的同级目录下还有个Counters，这个示例稍微复杂一点。<br/>
再往下看前，请打开你已经down下来的demo<br/>

先来看看文件目录：

```
-app
---actions
-----actionTypes.js : action类型
-----discolorActions.js : action creators

---components
-----discolor.js : 纯ReactNative自定义组件

---containers
-----app.js ：加入Provider
-----discolorApp.js ：定义app

---reducers
-----discolor.js : reducer之一
-----index.js : export所有reducer，当然这里只有一个discolor

-index.ios.js
-index.android.js
```

**安装依赖**此处为必要的安装，如果缺少，会有red screen提示的

```
    npm install --save redux
    npm install --save react-redux
```

**actions/actionTypes.js**定义了两种类型：变成红色 和 变成绿色

```
        export const CHANGE2RED = 'CHANGE2RED';
        export const CHANGE2GREEN = 'CHANGE2GREEN';
```

**actions/discolorActions.js**定义了两个action creator，如果你是从头看的，<br/>
现在应该能看懂了，如果不懂，那就从头看吧

```
        import * as types from './actionTypes';
        
        export function change2red() {
          return {
            type: types.CHANGE2RED,
          }
        }
        
        export function change2green() {
          return {
            type: types.CHANGE2GREEN,
          }
        }
```

**components/discolor.js**是RN的自定义控件

```
        import React, { Component } from 'react';
        import {
          StyleSheet,
          View,
          Text,
          TouchableHighlight,
        } from 'react-native';
        
        export default class Discolor extends Component {
          constructor(props) {
            super (props);
          }
        
          render() {
            // 这些属性是从DiscolorApp传过来的
            const { color, change2red, change2green} = this.props;
        
            return (
              <View style={[styles.container, {
                  backgroundColor: color,
              }]}>
                <TouchableHighlight onPress={change2red} style={styles.button}>
                  <Text>change2red</Text>
                </TouchableHighlight>
                <TouchableHighlight onPress={change2green} style={styles.button}>
                  <Text>change2green</Text>
                </TouchableHighlight>
              </View>
            );
          }
        }
        
        const styles = StyleSheet.create({
          container: {
            flex: 1,
            justifyContent: 'center',
            alignItems: 'center',
            backgroundColor: 'purple',
          },
          button: {
            width: 120,
            height: 40,
            backgroundColor: 'lightgray',
            alignItems: 'center',
            justifyContent: 'center',
            margin: 3,
          },
        });

```

**containers/discolorApp.js**：已经加入了必要的注释，不懂的留言吧

```
        'use strict' // javascript的严格模式
        
        import React, { Component } from 'react';
        import { bindActionCreators } from 'redux';
        import Discolor from '../components/discolor';
        import * as discolorActions from '../actions/discolorActions';
        import { connect } from 'react-redux';
        
        class DiscolorApp extends Component {
          constructor(props) {
            super(props);
          }
        
          render() {
            const { state, actions } = this.props;
            return (
              <Discolor
                color={state.color}
                {...actions} />
            );
          }
        }
        
        /*
         *
         * connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])
         *
         * 这里只说第一个参数，其他的参考：http://cn.redux.js.org/docs/react-redux/api.html
         *
         * [mapStateToProps(state, [ownProps]): stateProps] (Function): 如果定义该参数，组件将会监听 Redux store 的变化。
         * 任何时候，只要 Redux store 发生改变，mapStateToProps 函数就会被调用。
         * 该回调函数必须返回一个纯对象，这个对象会与组件的 props 合并。如果你省略了这个参数，你的组件将不会监听 Redux store。
         * 如果指定了该回调函数中的第二个参数 ownProps，则该参数的值为传递到组件的 props，而且只要组件接收到新的 props，mapStateToProps 也会被调用。
         *
         * 所以这里state: state.discolor中的discolor其实就是reducers/discolor.js
         * 一旦Redux store变化这个回调函数就会执行discolor，discolor就会返回新的state，
         * 为什么discolor会返回新的state，因为它是reducer
         */
        export default connect(state => ({
            state: state.discolor
          }),
          (dispatch) => ({
            /*
             * 再来说下 bindActionCreators 的作用: 把 action creators 转成拥有同名 keys 的对象，但使用 dispatch 把每个 action creator 包围起来，这样可以直接调用它们。
             * 什么意思呢？在这里的bindActionCreators返回值就是普通对象：
             *   {
             *     change2red: dispatch(discolorActions.change2red),
             *     change2green: dispatch(discolorActions.change2green),
             *   }
             */
            actions: bindActionCreators(discolorActions, dispatch),
          })
        )(DiscolorApp);

```

**containers/app.js**

```
        import React, { Component } from 'react';
        import { createStore, applyMiddleware, combineReducers } from 'redux';
        import { Provider } from 'react-redux';
        import thunk from 'redux-thunk';
        
        import * as reducers from '../reducers';
        import DiscolorApp from './discolorApp';
        
        /*
         *
         *  通过使用applyMiddleware来允许使用thunk
         *
         */
        const createStoreWithMiddleware = applyMiddleware(thunk)(createStore);
        /*
         *
         *  合并多个reducer
         *
         */
        const reducer = combineReducers(reducers);
        const store = createStoreWithMiddleware(reducer);
        
        export default class App extends Component {
          render() {
            return (
              <Provider store={store}>
                <DiscolorApp />
              </Provider>
            );
          }
        }
```

**reducers/discolor.js**：

```
        /*
         *
         *  项目里唯一的reducer
         *
         */
        
        import * as types from '../actions/actionTypes';
        
        const initialState = {
          color: 'red',
        };
        
        export default function discolor(state = initialState, action = {}) {
          switch (action.type) {
            case types.CHANGE2RED:
              return {
                ...state,
                color: 'red',
              };
            case types.CHANGE2GREEN:
              return {
                ...state,
                color: 'green',
              };
            default:
              return state;
          }
        }
```

**reducers/index.js**：为了可以统一导出所有reducer，这里只有discolor<br/>
如何统一导出，在*containers/app.js*中是这么用的： *import * as reducers from '../reducers';*

```
        import discolor from './discolor';
        
        export {
          discolor,
        };
```

**index.ios.js**：代码与index.android.js相同

```
    'use strict' // javascript严格模式
    
    import React from 'react';
    import { AppRegistry }  from 'react-native';
    import App from './app/containers/app'
    
    AppRegistry.registerComponent('ReactNativeReduxSimplest', () => App);
```

# 结语

以上就是关于ReactNative结合Redux的入门教程，希望可以帮助大家理清思路。我本着用最简单的方式表达最重要的信息的目的来撰写此文，在有些措辞不严谨或者表达不够准确的地方希望大家可以留言。

# 参考

> [redux 英文版本](http://redux.js.org/)：如果你不喜欢请看参考2<br/>
> [redux 中文版本](http://cn.redux.js.org/docs/basics/index.html)：翻译版，系统讲解<br/>
> [stackoverflow问题](http://stackoverflow.com/questions/29722270/import-modules-from-files-in-directory)：ES6能否import一个目录（多个文件）<br/>
> [Redux Thunk](https://github.com/gaearon/redux-thunk): 什么是Thunk？<br/>
> [example-react-native-redux](https://github.com/alinz/example-react-native-redux) : demo参考了这个项目

[1]: http://redux.js.org/ "redux 英文版本"
[2]: http://cn.redux.js.org/docs/basics/index.html "redux 中文版本"
[3]: http://stackoverflow.com/questions/29722270/import-modules-from-files-in-directory
 "ES6能否import一个目录（多个文件）"
[4]: https://github.com/gaearon/redux-thunk "什么是Thunk？"
[5]: https://github.com/alinz/example-react-native-redux "example-react-native-redux，demo参考了这个项目"
[6]: https://github.com/CoderGLM/react-native-redux-example/tree/master/Discolor "Discolor"
