---
title: React父组件调用子组件方法
date: 2018-05-30 17:40:23
tags:
- React
categories: React # 分类
thumbnail: http://o835lhtor.bkt.clouddn.com/blog/img/3.jpg # 略缩图
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
