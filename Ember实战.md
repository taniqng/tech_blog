# 关于Ember

Ember是一个前端MVC框架，它和React以及VUE的对比，这里不作介绍，在国内人气也远不及更容易上手的VUE，但是作为Yehuda Katz的忠诚粉丝，无条件支持他在前端领域开辟的新疆土。如果你了解rails，也读过Jquery in action，会对Ember有莫名的情怀，然而对于ember，不仅仅是情怀。

## 为何讲Ember？

* 渲染性能：glimmer引擎提供近似原生程序的体验（https://auth0.com/blog/face-off-virtual-dom-vs-incremental-dom-vs-glimmer/）
* 完善的文档
* 约定优于配置使你无条件的继承着js大神的编程习惯，并在此中受益。
* Ember CLI和丰富的addon
* 美妙的编程之旅，一起看下面

## 从零开始

### 安装NodeJS
### 安装Ember CLI
```
npm install -g ember-cli
```
### 创建app并生成脚手架
```
ember new tech-navi
```
### 创建一个路由
```
ember g route projects 
```
#### 请求数据流
graph LR
   Request --> Route  --> Controller --> Tempates（VIEW）
