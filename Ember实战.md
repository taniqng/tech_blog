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
#router.js
import EmberRouter from '@ember/routing/router';
import config from './config/environment';

const Router = EmberRouter.extend({
  location: config.locationType,
  rootURL: config.rootURL
});

Router.map(function() {
  this.route('departments', function() {
    this.route('department',{ path: '/:department_id' });
  });
  this.route('projects', function() {
    this.route('project', { path: '/:project_id' });
  });
});

export default Router;


#projects.js routes
import Route from '@ember/routing/route';

export default Route.extend({
    
    queryParams: {
        departmentId: {
          refreshModel: true
        }
      },
    model: function(args){
        return this.store.findAll("project");
    }
});
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
import DS from 'ember-data';

export default DS.Model.extend({

    projectName: DS.attr('string'),
    projectLogo: DS.attr('string'),
    projectEnName: DS.attr('string'),
    projectLink: DS.attr('string'),
    projectWikiLink: DS.attr('string'),
    owner: DS.attr('string'),
    others: DS.attr('string'),
    ownerAvatar: DS.attr('string'),
    deptId:DS.attr('string'),
    desc:DS.attr('string'),
    status:DS.attr('string')

});
```

### 在Route里加载数据
刚才提到了，route负责加载数据，一般而言都是会调用后台的rest服务。
route的model hook里调用this.store.findAll("project");这会调用后台的[GET] http://domain/projects
```
import Route from '@ember/routing/route';

export default Route.extend({
    
    queryParams: {
        departmentId: {
          refreshModel: true
        }
      },
    model: function(args){
        return this.store.findAll("project");
    }
});
```
model hook返回一个promise，当promise数据加载完成会将实际的数据set到与之对应的controller里。
### 在Controller里准备要展示的数据
```
#描述的是一个部门department所维护的工程（projects）列表。
import Controller from '@ember/controller';
import { computed } from '@ember/object';

export default Controller.extend({
    queryParams: ['departmentId'],
    departmentId: null,
    keywords:"",
    
    #根据部门过滤projects
    projects: computed('model', function() {

      let projs = this.get('model');
      let thiz = this;
      let deptId = thiz.get('departmentId') || "";
      let projsForThisDept = projs.filter(function(value){
          return value.get('deptId').startsWith(deptId);
      });
      return projsForThisDept;
      
    }),
    
    #忽略大小写过滤某关键字，filteredProjects可以在template中直接使用，注意该变量会双向绑定到template
    filteredProjects: computed('projects','keywords', function() {

      let projs = this.get('projects');
      let keywords = this.get('keywords');
      let filteredProjs = projs.filter(function(value){
        let pname = value.get('projectName');
        let ename = value.get('projectEnName');
        let desc = value.get('desc');

        let isHit = pname&&pname.toLowerCase().indexOf(keywords.toLowerCase())!=-1
              || ename&&ename.toLowerCase().indexOf(keywords.toLowerCase())!=-1
              || desc&&desc.toLowerCase().indexOf(keywords.toLowerCase())!=-1;
        return isHit;
      });
      return filteredProjs.sortBy('owner','projectName','projectEnName');
      
    })
});

```
### 在Template布局页面和展示数据
```
<div class="panel panel-default">
  <!-- Default panel contents -->
  <div class="panel-heading"><b>项目列表</b> {{#if departmentId}}( {{dept-helper departmentId}} ) {{/if}}<a href="#"><span class="badge">{{filteredProjects.length}}</span></a></div>
  <div class="panel-body">
    <div class="input-group">
        <span class="input-group-addon">搜索</span>
        {{input type="text" value=this.keywords class="form-control" placeholder="请输入关键字..."}}
        
        {{#if keywords}}
        <span class="input-group-addon">keywords: "{{this.keywords}}"</span>
        
        {{/if}}
    </div>
  </div>

  <!-- Table -->
  <table class="table table-striped">
  <thead>
    <tr>
      <th scope="col" width="10%"><b>项目昵称</b></th>
      <th scope="col" width="10%"><b>项目名</b></th>
      <th scope="col" width="20%"><b>描述</b></th>
      <th scope="col" width="8%"><b>负责人</b></th>
      <th scope="col" width="10%"><b>状态</b></th>
      <th scope="col" width="10%"><b>link</b></th>
      <th scope="col" width="20%"><b>文档</b></th>
      <th scope="col" width="12%"><b>操作</b></th>
    </tr>
  </thead>
  <tbody>
    
        {{#each this.filteredProjects as |proj index|}}
            <tr>
            <td>{{proj.projectEnName}}</td>
            <td>{{projectName}}</td>
            <td>{{proj.desc}}</td>
            <td>{{proj.owner}}</td>
            <td>{{proj.status}}</td>
            <td><a href="{{proj.projectLink}}" target="_blank">{{proj.projectLink}}</a></td>
            <td><a href="{{proj.projectWikiLink}}" target="_blank">{{proj.projectWikiLink}}</a></td>
            <td>
                <div class="btn-group">
                    <button type="button" class="btn btn-success btn-sm">Action</button>
                    <button type="button" class="btn btn-success dropdown-toggle btn-sm" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                        <span class="caret"></span>
                        <span class="sr-only">Toggle Dropdown</span>
                    </button>
                    <ul class="dropdown-menu">
                        <li><a href="#">Action</a></li>
                        <li><a href="#">Another action</a></li>
                        <li><a href="#">Something else here</a></li>
                        <li role="separator" class="divider"></li>
                        <li><a href="#">Separated link</a></li>
                    </ul>
                </div>
            </td>
            </tr>
        {{/each}}
    
  </tbody>
</table>
</div>

{{outlet}}
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
<div class="panel panel-default">
  <!-- Default panel contents -->
  <div class="panel-heading"><b>项目列表</b> {{#if departmentId}}( {{dept-helper departmentId}} ) {{/if}}<a href="#"><span class="badge">{{filteredProjects.length}}</span></a></div>
  <div class="panel-body">
    <div class="input-group">
        <span class="input-group-addon">搜索</span>
        {{input type="text" value=this.keywords class="form-control" placeholder="请输入关键字..."}}
        
        {{#if keywords}}
        <span class="input-group-addon">keywords: "{{this.keywords}}"</span>
        
        {{/if}}
    </div>
  </div>

  <!-- Table -->
  <table class="table table-striped">
  <thead>
    <tr>
      <th scope="col" width="10%"><b>项目昵称</b></th>
      <th scope="col" width="10%"><b>项目名</b></th>
      <th scope="col" width="20%"><b>描述</b></th>
      <th scope="col" width="8%"><b>负责人</b></th>
      <th scope="col" width="10%"><b>状态</b></th>
      <th scope="col" width="10%"><b>link</b></th>
      <th scope="col" width="20%"><b>文档</b></th>
      <th scope="col" width="12%"><b>操作</b></th>
    </tr>
  </thead>
  <tbody>
    
        {{#each this.filteredProjects as |proj index|}}
            <tr>
            <td>{{mark-keywords proj.projectEnName keywords}}</td>
            <td>{{mark-keywords proj.projectName keywords}}</td>
            <td>{{mark-keywords proj.desc keywords}}</td>
            <td>{{proj.owner}}</td>
            <td>{{proj.status}}</td>
            <td><a href="{{proj.projectLink}}" target="_blank">{{proj.projectLink}}</a></td>
            <td><a href="{{proj.projectWikiLink}}" target="_blank">{{proj.projectWikiLink}}</a></td>
            <td>
                <div class="btn-group">
                    <button type="button" class="btn btn-success btn-sm">Action</button>
                    <button type="button" class="btn btn-success dropdown-toggle btn-sm" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                        <span class="caret"></span>
                        <span class="sr-only">Toggle Dropdown</span>
                    </button>
                    <ul class="dropdown-menu">
                        <li><a href="#">Action</a></li>
                        <li><a href="#">Another action</a></li>
                        <li><a href="#">Something else here</a></li>
                        <li role="separator" class="divider"></li>
                        <li><a href="#">Separated link</a></li>
                    </ul>
                </div>
            </td>
            </tr>
        {{/each}}
    
  </tbody>
</table>
</div>

{{outlet}}
```


# 后记


 
