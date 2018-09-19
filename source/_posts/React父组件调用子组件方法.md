---
author: 
  nick: Seven
  link: https://blog.csdn.net/sweiqin
title: React父组件调用子组件方法
date: 2018-05-30 17:40:23
tags:
- React
categories: React # 分类
cover: http://o835lhtor.bkt.clouddn.com/blog/img/react.png # 略缩图

subtitle: 有很多情况下,需要父子组件的方法相互调用,这样就需要父子组件进行通信.
---
### 代码块
```javascript
import React, {Component} from 'react';

export default class Root extends Component {
    render() {
        return(
            <div>
                <Child onRef={this.onRef} />
                <Button onClick={this.onClick} >通信</Button>
            </div>
        )
    }

    onRef = (ref) => {
        this.child = ref
    }

    onClick = (e) => {
        this.child.onClick()
    }

}

class Child extends Component {
    componentDidMount(){
        this.props.onRef(this)
    }

    onClick = () => console.info("父子通信OK")

    render() {
        return (<div>父子通信</div>)
    }
}
```
