---
layout: post
title: React/ReactNative中ES5和ES6语法的区别
date: 2016.02.23 12:50.000000000 +09:00
category: 前端相关
description: React/ReactNative中ES5与ES6的区别
tags: 前端;javascript;;
---


![image]({{ site.imageurl }}/assets/images/2016/difference-es5-es6-in-react.png)

## 前言

> 在github上有很多框架都是用的ES5，对于接触ES5比较多的人，如果想写ES6语法还是要有个参考，
> 这里用ES5/ES6实现了两段功能完全相同的代码，但是不保证可以正常运行，主要是把区别表现出来。

## ES5
```
// ES 5
var React = require("react-native");
var {
  Image,
  Text,
} = React;

var MyComponent = React.createClass({
  getDefaultProps: function() {
    return {
      prop1: value1,
      prop2: value2
    }
  },

  propTypes: {
    prop1: React.PropTypes.number.isRequired,
    prop2: React.PropTypes.string.isRequired
  },

  getInitialState: function() {
    return {
      state1: this.props.state1
    }
  },

  componentWillMount: function() {

  },

  render: function() {
    return (
      // 不需要bind，因为ES5内部会bind
      <TouchableHighlight onPress={this.handlePress}>
        <Image source={this.props.source} />
      </TouchableHighlight>
    );
  },

  handlePress:function() {
  }
});

module.exports = MyComponent;

```

## ES6
```
// ES6
import React from 'react';
import {
  Image,
  Text,
} from 'react-native';

// 如果只需要导出一个对象就用export default，导出多个用export
export default class MyComponent extends React.Component {
  static defaultProps = {
    prop1: value1,
    prop2: value2,
  }

  static propTypes = {
    prop1: React.PropTypes.number.isRequired,
    prop2: React.PropTypes.string.isRequired
  }

  constructor(props) {
    super(props);

    this.state = {
      state1: this.props.state1,
    };
  }

  componentWillMount() {
  }

  render() {
    return (
      // 这里需要bind，或者使用jian tjian t箭头函数
      <TouchableHighlight onPress={this.handlePress.bind(this)}>
        <Image source={this.props.source} />
      </TouchableHighlight>
    );
  }

  handlePress() {
  }
}

```
