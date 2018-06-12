# 2018-5-13
* 创建项目时顺便创建路由定义文件 `ng new 项目名称 --routing`
* 在项目中使用第三方包
```
# 1、使用npm命令安装包 并用--save命令将项目对包的依赖关系写进package.json
npm install 包名 --save
# 2、编辑 /angular.json 文件，比如我要使用bootstrap，就需要在 projects.architect.build.styles 和 ...script 中声明bootstrap文件的 css 和 js 地址
...
    "styles": [
        "src/styles.css",
        "node_modules/bootstrap/dist/css/bootstrap.min.css"
    ],
    "scripts": [
        "node_modules/jquery/dist/jquery.min.js",
        "node_modules/bootstrap/dist/js/bootstrap.min.js"
    ]
...
```
* 创建一个新的组件 `ng g c 组件名`
* 给组件添加一条路由 src/app/app-routing.module.ts
```
// 导入组件
...
import { 组件类名 } from './组件文件夹/组件类定义文件'

// 路由定义 一个常量数组
const routes: Routes = [
  // {path: '路由地址', component: 跳转的组件}

  // 默认路由 redirectTo: '重定向到路由', pathMatch: 'full' 必须要写
  {path: '', redirectTo: '/News', pathMatch: 'full'}
];
```
* 在视图上实现路由 和 路由出口标签的使用
```
<!-- 不用href, 改用routerLink -->
<a routerLink="/路由地址"> xxx </a>

<!-- 路由出口：即点击路由链接后，根据其指向的组件展示组件模板的一个标签 -->
<router-outlet></router-outlet>
```
* 在视图上的一些操作
```
# 选择一个宿主元素，遍历对象集合
<li *ngFor="let obj of objs">
    # 展示对象的具体属性
    {{obj.property}}
</li>

# 选择一个宿主元素，进行某些判断，结果为true时才显示这个元素
<div *ngIf="条件表达式">
</div>

# 给某个元素绑定点击事件
<button (click)="fnName()"></button> #这个fnName()就是我们在组件定义里面编写的函数
```
