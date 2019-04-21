# 关于Ember

Ember是一个前端MVC框架，它和React以及VUE的对比，这里不作介绍，在国内人气也远不及更容易上手的VUE，但是作为Yehuda Katz的忠诚粉丝，无条件支持他在前端领域开辟的新疆土。如果你了解rails，也读过Jquery in action，会对Ember有莫名的情怀，然而对于ember，不仅仅是情怀。

## 为何讲Ember？

* 渲染性能：glimmer引擎提供近似原生程序的体验（https://auth0.com/blog/face-off-virtual-dom-vs-incremental-dom-vs-glimmer/）
* 完善的文档
* 约定优于配置使你无条件的继承着js大神的编程习惯，并在此中受益。
* Ember CLI和丰富的addon
* 组件化开发（定义各种自定义组件，无差别复用）
* 自定义helper避免template/controller里的一切冗余代码
* 自定义Service实现Service的复用

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

### 集成bootstrap
```
#集成bootstrap
ember install ember-bootstrap
```

好了，我们可以直接在template里使用bootstrap样式了。

### 创建一个路由
```
ember g route projects 
```
会生成如下代码：
```
1
```
#### 请求数据流（Ember三个核心概念）
Ember三个核心角色分别是，Route、Controller、Template。
* Tempalte就是我们的VIEW层（html模板），在这里布局，画html。
* Controller是Template的上下文，我们可以在template里使用Controller里的所有数据
* Route负责准备Controller里的数据（对后端的ajax请求一定要写在这里）。

 Request --> Route  --> Controller --> Tempates（VIEW）
 
 Ember会自动按照下面的命名规范创建Router， Controller（即使你不创建）

### Model
   model是一个业务对象的抽象，一般对应后台的一个model。
```
ember g model project
```
上面代码会自动生成如下代码，我们可以定义model的fields。
```
2
```

### 在Route里加载数据
刚才提到了，route负责加载数据，一般而言都是会调用后台的rest服务。
route的model hook里调用this.store.findAll("project");这会调用后台的[GET] http://domain/projects
```
3
```
model hook返回一个promise，当promise数据加载完成会将实际的数据set到与之对应的controller里。
### 在Controller里准备要展示的数据
```
4
```
### 在Template布局页面和展示数据
```
5
```
如果我们想把搜索到的关键字红色字体展示，可以写一个helper
```
ember g helper mark-keywords
```
生成如下代码，我们完成红色字体的处理逻辑
```
#helper
```
在template里使用helper
```
6
```


# 后记


 
