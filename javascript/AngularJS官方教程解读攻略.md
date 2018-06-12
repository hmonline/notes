# 攻略说明
* 这是一篇来自angular官方的入门教程，构建一个“管理英雄”的应用。[详情](https://www.angular.cn/tutorial)
* 官网教程有太多专业名词，可能不适用于不了解一些概念的新手朋友（包括我自己），所以我照着官方入门教程的说明来一步步构建这个项目，同时记录我的开发过程，加入我自己的“大白话理解”。

# 安装nodeJS(npm)
```
# 这是用curl 添加源
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
# 这是安装
sudo apt-get install -y nodejs
# 这是个坑，反正我上次用cnpm装的脚手架工具写出来得代码不不能编译的，不过如果你愿意试试，可以装个cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

# 什么是angularJS
* 为什么要用angularJS： 前端框架，到目前为止，我只用过bootstrap（解决css问题）和jquery（解决动画和Ajax请求）。用他们俩搭配开发的前端html页面(通常是我用在php框架中的视图)有以下几个缺点
    1. 页面很多：比如一个blog项目，我得有index.html主页，admin.html后台页，list.html博客列表页，article.html文章页以及通用的，或者某个界面单独的css、js。
    2. 纯静态导致高耦合，在php框架中(thinkphp | laravel)，比如我希望某个控制器查询数据库，并将数据分页显示在某个视图上，如果我给视图传递的变量名叫$datas，那么我在视图必须去遍历的对象名字也就要$datas，没有做到真正的前后端分离（真正的项目开发中，万一只要求我处理控制器层的逻辑，我是不是得去问前端给我提供的视图的html结构是什么样的。或者只要求我去处理视图，我是不是得和后端沟通，问它给我的数据的结构是怎么样的，我才好在视图层合理美观的显示这些数据）
* angularJS如何解决这些问题的：angularJS由TS语言编写，实现了“前端工程化”，前端写页面，后端写API，前后端用json（大部分时候）沟通即可。
    1. 解决页面很多：单页应用组件化，仔细想想，很多时候我之前使用php框架提供的视图也是在模块化视图：比如我的导航nav不变，header头不变，footer脚不变，唯一变的就是div#content内容。那么我把nav header footer写死写在布局模板中，其它模板继承布局模板，然后编辑div#content的内容即可。其实angular的组件化也是这个逻辑：我们可以把div#content看作一个组件（angular模板上的一个特定的标签），这个组件实现渲染某一区域不同的内容即可。
    2. 实现前后端分离： 上面说过了，前端处理用户请求和响应。后端提供API即可（每个控制器的每个方法return的都是json对象）。

# 安装cli脚手架 创建项目 启动服务以预览项目
* 安装cli脚手架 `npm install -g @angular/cli`
* 创建项目 `ng new angular-heroes`
* 启动服务 
```
# 进入项目目录
cd angular-heroes 
# 启动：默认可以在 localhost:4200 访问
ng serve
```

# 壳组件 AppComponent
* 了解“组件3要素” => 模板.html / 样式.css / 类定义.ts （都位于 src/app/ 下）
    * 模板叫 app.component.html
    * 样式叫 app.component.css
    * 类定义叫 app.component.ts
* 模板和样式我们已经非常了解了，很简单的html和css代码，重点是类定义，类定义分3块
```
// 这是第1块：导入核心类库 @angular/core 提供的组建类 Component （我喜欢把它叫导包，因为和java很像，同时实现的功能也类似php中的use 空间类元素）
import { Component } from '@angular/core';

// 这是第2块： 装饰器，这里装饰了Component(可以理解为在配置这个组件)
@Component({
    selector: 'app-root', //告诉组件要渲染的具体标签
    templateUrl: './app.component.html', //告诉组件视图模板存在的位置
    styleUrls: ['./app.component.css'] //告诉组件css样式的位置
})

// 这是第3块： 具体的类定义，我们把变量、函数等等都写在这里面
export class AppComponent { // AppComponent 就从这里来
    title = 'app';
}
```
* 简单的“玩一玩”这个组件
    * 在 类定义.ts 中修改 title 的值
    ```
    export class AppComponent {
       title = '远走最帅'; //你可以写你想写的东西
    }
    ```
    * 在 模板.html 中 **插值** => 页面显示“远走最帅”
    ```
    <h1>{{ title }}</h1> <!-- 这里的title就对应上面类定义中的title -->
    ```
    * 在 样式.css 中定义一些 样式 并使用它 => 页面显示紫色的“远走最帅”
    ```
    #css
    .handSomeName {
        color: purple;
        font-weight: bold;
    }

    #视图
    <h1 class="handSomeName">{{ title }}</h1>
    ```
    > 类定义.ts处理数据 / 模板.html展示数据 / 样式.css修饰模板 => 这就是一个组件Component（Angular生成的页面中的最小单位）
* 回归主题，照着官方教程，来构建应用 => 这里做的跟我们上面一样，类定义中编辑一个变量title的值，在视图层用{{}}展示，然后写一个css修饰。（官网教程中提供了它写好的css，可以直接copy它的）
* 这里的“壳组件” 可以理解为 “组件的根”，“程序的入口”，“index.html”。即我们访问angular应用时载入的第一个组件，执行的第一个程序。同时，可以将它的模板看作“布局模板”，样式看作“基础样式”。

# 第一个子组件 HeroesComponent
* 组件具体作用： 展示英雄列表。
* 创建组件 `ng generate component heroes`
* 上面的命令可以简写为 `ng g c heroes`
* 这时候在 src/app/ 下生成了 heroes/ 目录，打开一看，没有问题，还是“组件3要素”，模板 样式 类定义。
* 主要看看类定义 **@Component.selector**
```
// 1导包
import { Component, OnInit } from '@angular/core';

// 2装饰器
@Component({
  selector: 'app-heroes', //再次注意这一句：告诉我们该组件渲染的标签
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})

// 3具体类定义
export class HeroesComponent implements OnInit {
  // 请暂时忽略构造函数
  constructor() { }
  
  // 请暂时忽略初始化函数
  ngOnInit() {
  }

}
```
* 在“布局模板” app.component.html 中导入子组件 => 页面多显示 heroes works! （来自于 heroes.component.html）
```
<h1>{{ title }}</h1>
<app-heroes></app-heroes>
```
* 模拟英雄数据
    * 在 src/app/ 下创建一个 hero.ts （创建“规范类”）
    ```
    export class Hero {
        // 制定规范
        id: number; //id: 得是数字
        name: string; //name: 得是字符串
    }
    ```
    * 在 src/app/ 下创建一个 mock-heroes.ts （创建模拟的“英雄列表”，实际应用中我们应该从后台应用程序接口获取这类数据）
    ```
    // 导入规范
    import { Hero } from "./hero";

    // 实例化一个常量数组
    export const HEROES: Hero[] = [
    // const定义常量 HEROES常量名: 是一个Hero类实例化对象[]的集合
        { id: 11, name: 'Mr. Nice' },
        { id: 12, name: 'Narco' },
        { id: 13, name: 'Bombasto' },
        { id: 14, name: 'Celeritas' },
        { id: 15, name: 'Magneta' },
        { id: 16, name: 'RubberMan' },
        { id: 17, name: 'Dynama' },
        { id: 18, name: 'Dr IQ' },
        { id: 19, name: 'Magma' },
        { id: 20, name: 'Tornado' }
    ];
    ```
    > 主要是注意语法：“变量: 数据类型”

* HeroesComponent 导入模拟的数据，在模板中显示，然后美化模板
    * 类定义.ts中导入数据
    ```
    // 第1块中 导入 我们模拟的 英雄列表
    ...
    import { HEROES } from '../mock-heroes'; //导入数据

    // 第3块中 将数据赋值给变量heroes
    export class HeroesComponent implements OnInit {
        // 将导入的数据复制给一个变量 heroes
        heroes = HEROES;

        constructor() { }

        ngOnInit() {
        }
    }
    ```
    * 在模板中显示数据
    ```
    <h2>My Heroes</h2>
    <ul class="heroes">
        <!-- *ngFor="let 单个对象 of 对象数组即我们在类定义中定义的heroes" -->
        <li *ngFor="let hero of heroes">
            <!-- 单个对象.具体属性 -->
            <span class="badge">{{hero.id}}</span> {{hero.name}}
        </li>
    </ul>
    ```
    * 美化视图，copy官方教程提供的css，复制到heroes.component.css

# HeroesComponent “点击事件”，展示英雄详情。
* 模板绑定点击事件 
```
<!-- (click)="触发函数onSelect(参数hero)" -->
<li *ngFor="let hero of heroes" (click)="onSelect(hero)">
```
* 类定义中编辑 onSelect() 函数
```
  // 定义一个变量 被选中的英雄: Hero类型的
  selectedHero: Hero; //注意：此时 selectedHero = undefined
  // 定义一个 onSelect() 方法 给 selectedHero 赋值
  onSelect(hero): void { //函数名(参数列表): 返回值类型 {函数体}
    this.selectedHero = hero; //注意：此时 selectedHero = 参数传递进来的hero
  }
```
* 视图中展示被选中的英雄的“详情”
```
<!-- 英雄详情 -->
<!-- *ngIf="selectedHero有值即为true" -->
<div *ngIf="selectedHero">
  
  <!-- 左边的值 | 调用右边的管道 uppercase全部大写 --> 
  <h2>{{ selectedHero.name | uppercase }} Details</h2>
  <div><span>id: </span>{{selectedHero.id}}</div>
  <div>
    <label>name:
      {{selectedHero.name}}
    </label>
  </div>

</div>
```

> 绑定点击事件 (click)="函数(参数)"

> *ngIf="判断条件" 为true 则展示div和div内部的数据

> {{value | grep}} => 调用管道函数, uppercase将字符串字符全部转换为大写字母显示

* 排错经验
```
# Chrome控制台报错 _co.onSelect is not a function（onSelect不是一个函数）
# 找了半天，原来是 onSelect() 函数名写成了 onselect() s小写了
```

# HeroesComponent “双向数据绑定”
* 先了解一下“模块”：位于 src/app/app.module.ts 由多个组件组成的东西：和组件大同小异的 “3部分”
```
// 1导包
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';
import { HeroesComponent } from './heroes/heroes.component';

// 2装饰器
@NgModule({
  // 使用的组件
  declarations: [
    AppComponent,
    HeroesComponent
  ],
  // 使用的其他模块
  imports: [
    BrowserModule
  ],
  // 服务提供
  providers: [],
  // “引导组件”
  bootstrap: [AppComponent]
})

// 3类定义
export class AppModule { }

```
* 在AppModule中导入一个模块 FromsModule
```
// 1
...
import { FormsModule } from '@angular/forms'; //导入FormsModule

// 2在装饰器中真正投入使用
  ...
  imports: [
    BrowserModule,
    FormsModule //让FormsModule真正投入使用
  ],
  ...
```
* 在视图中实现双向数据绑定：[(ngModel)]="被绑定的数据"
```
<div>
    <label>name:
    <!-- [(ngModel)]="被绑定的数据" -->
    <input [(ngModel)]="selectedHero.name" placeholder="name">
    </label>
</div>
```

> 这里把 selectedHero.name 属性绑定到了 HTML 的 input 元素上，以便数据流可以双向流动：从 selectedHero.name 属性流动到 input，并且从 input 流回到 selectedHero.name 。

# 将“英雄详情” 作为一个单独的小组件
* 创建组件 `ng g c hero-detail`
* 分离视图，将HeroesComponent视图模板中的“英雄详情部分”剪切到HeroDetailComponent的模板中，将'selectedHero'全部替换为'hero' (因为这个组件不再仅用于展示"被选中的"英雄的详情了)
```
<!-- 英雄详情 -->
<!-- *ngIf="hero有值即为true" -->
<div *ngIf="hero">

  <!-- 左边的值 | 调用右边的管道 uppercase全部大写 --> 
  <h2>{{ hero.name | uppercase }} Details</h2>
  <div><span>id: </span>{{hero.id}}</div>
  <div>
      <label>name:
        <!-- [(ngModel)]="被绑定的数据" -->
        <input [(ngModel)]="hero.name" placeholder="name">
      </label>
  </div>

</div>
```
* **重点部分** ： 使用核心库提供的 Input 类，实现了HeroesComponent.selectedHero与HeroDetailComponent.here的数据绑定
    * 在 HeroDetailComponent 中导入 Input 和 Hero规范类 ，并定义一个 @Input() 属性
    ```
    // 1
    ...
    import { Component, OnInit, Input } from '@angular/core'; //Input在核心库中 所以直接在这里导入
    import { Hero } from '../hero'; //规范类

    // 3
    ...
    export class HeroDetailComponent implements OnInit {

        @Input() hero: Hero; //这样定义该属性以在两个组件之间实现数据绑定

        constructor() { }

        ngOnInit() {
        }

    }
    ```
    * 在 HeroesComponent 的模板中 HeroDetail中的**[hero]** 等于 Heroes中的**"selectedHero"**
    ```
    <!-- 被选择英雄详情 -->
    <app-hero-detail [hero]="selectedHero"></app-hero-detail>
    ```

    > 莫名其妙的，AppModule崩了，不知道为什么有的字符串跑另一行去了，自己照着语法改了下才正常了。

    > 分离子组件的好处就是 1提高HeroDetailComponent的复用性 2减少HeroesComponent的耦合

# 服务
* 创建服务hero `ng g s hero`
* 创建的服务位于 /src/app/hero.service.ts , 同样是3部分
```
// 1导包
import { Injectable } from '@angular/core';

// 2装饰器
@Injectable({
  providedIn: 'root'
})

// 3具体定义
export class HeroService {

  constructor() { }
}
```
* 在服务中，我们依然使用模拟的数据
```
// 1
...
import { Hero } from "./hero"; //导入规范类
import { HEROES } from "./mock-heroes"; //导入模拟的数据

// 3
export class HeroService {

  // 定义一个方法：获取所有的英雄数据
  getHeroes(): Hero[] {
    return HEROES;
  }

  constructor() { }
}
```
* 将服务注册到 AppModule src/app/app.module.ts
```
// 1
import { HeroService } from "./hero.service"; //导入HeroService

// 2
...
  // 服务提供
  providers: [
    HeroService, //让HeroService真正投入使用
  ],
...
```
* 其实我们创建的时候可以通过命令完成服务注册 `ng g s hero --module=app`
* 现在我们由HeroService为我们提供数据，所以在HeroesComponent中，我们不需要常量“英雄列表“ HEROES，删除导入它的代码。在构造函数中，使用”依赖注入DI“，构建HeroService。变量 heroes ”英雄列表“的获取方式修改为通过函数 getHeroes() 获取。ngOnInit()方法中调用getHeroes() 方法，使组件被调用时，就能获取英雄列表
```
// 1删除 HEROES 的导入 导入服务 HeroService
...
import { HeroService } from "../hero.service"; //导入服务

// 3修改 heroes获取的过程
export class HeroesComponent implements OnInit {
  heroes: Hero[]; //修改heroes 这里只声明它的数据类型为 Hero类所实例化对象的集合

  // 构造函数中使用依赖注入的方式 实例化一个私有变量 heroService 为 HeroService 对象
  constructor(private heroService: HeroService) { }

  ngOnInit() {
    this.getHeroes(); //在初始化组件时获取”英雄列表“
  }

  // 函数： 获取英雄列表
  getHeroes(): void {
    this.heroes = this.heroService.getHeroes();
  }

  // 定义一个变量 被选中的英雄: Hero类型的
  selectedHero: Hero; //注意：此时 selectedHero = undefined
  // 定义一个 onSelect() 方法 给 selectedHero 赋值
  onSelect(hero): void { //函数名(参数列表): 返回值类型 {函数体}
    this.selectedHero = hero; //注意：此时 selectedHero = 参数传递进来的hero
  }
}
```
* 注意上面的过程是一直同步进行的：HeroesComponent组件被加载的时候，实例化 heroService ， 初始化函数 ngOninit() 调用组件内定义的 getHeroes() 函数， getHeroes() 函数其实是函数内部调用了 HeroService 提供的 getHeroes() 函数。但是：我们的数据是模拟的，可以保证数据100%获得（因为我们直接写死写在了本地），但是实际开发中，这些数据应该是后台提供的，所以我们从加载组件到通过组件调用服务，通过服务获取数据，最后显示在页面上，是一定会等待的。（也就会出现浏览器白屏等待数据加载的过程），为了提升体验，我们应该是异步完成这些动作的，所以我们使用 Observable 的方式获取数据
    * HeroService 服务导入 Observable
    ```
    // 1
    ...
    import { Observable, of } from "rxjs"; //导入 Observable 和 of() 函数

    // 3
    export class HeroService {

        // 定义一个方法：获取所有的英雄数据
        getHeroes(): Observable<Hero[]> { //这里声明数据类型为 Observable<Hero[]> “可观察的Hero对象集合”
            return of(HEROES); //这里用of(HEROES)返回数据
        }

        constructor() { }
    }
    ```
    * 在HeroComponent中订阅
    ```
    // 函数： 获取英雄列表
    getHeroes(): void {
        this.heroService.getHeroes()
            // 订阅数据 heroes 对应 本组件内的heroes
            .subscribe(heroes => this.heroes = heroes);  
    }
    ```

    > 同步服务：创建服务->AppModule中注册（providers）->组件中导入(constructor())

    > 异步服务：从 rxjs 导入 Observable, of ->修改类型为可观察的 Observable<变量类型> -> 返回数据用 of(数据) ->在组件导入用 .subscribe() 订阅

# 显示信息
* 具体作用：在页面下方显示提示信息
* 新建组件MessagesComponent `ng g c messages`
* 在壳组件的“布局模板” app.component.html 中添加该组件 => 页面多显示 messages works!
```
<h1>{{title}}</h1>
<app-heroes></app-heroes>
<app-messages></app-messages>
```
* 新建服务 MessageService 并注册到模块中 `ng g s message --module=app`
> 我在创建组件的时候发现 cli 自动帮我导入的代码有问题，有的代码跑到另外一行去了，可能是因为我写过注释，自己排过板的原因导致自动写入的代码不正确

> 在创建服务并注册的时候，我协商了--module=app 也不能写如AppModule中，只有我自己去导包然后在服务提供中声明
* 编辑 MessageService
```
// 3
export class MessageService {
  
  constructor() { }

  // 定义信息变量
  messages: String[] = [];

  // 定义添加信息方法
  add(message: string) {
    this.messages.push(message); //在数组中添加元素
  }

  // 定义清空信息方法
  clear() {
    this.messages = [];
  }
}
```
* 编辑 HeroService 让它可以发送一条提示信息
```
// 1
import { MessageService } from "./message.service"; //导入MessageService

// 3 
...
// 构造函数中 依赖注入 实例化 MessageService
constructor(private messageService: MessageService) { }

getHeroes(): Observable<Hero[]> { //这里声明数据类型为 Observable<Hero[]> “可观察的Hero对象集合”
    this.messageService.add('提示信息： 查询英雄成功'); //当获取数据成功的时候，添加一条提示信息
    return of(HEROES); //这里用of(HEROES)返回数据
} 
...
```
* 编辑 MessagesComponent
```
// 1
...
import { MessageService } from "../message.service"; //导入服务

// 3
export class MessagesComponent implements OnInit {
  //依赖注入： 实例化服务
  constructor(public messageService: MessageService) { } // 注意这里得是公开的属性：Angular 只会绑定到组件的公共属性。

  ngOnInit() {
  }

}

```
* 视图显示信息 messages.component.html
```
<div *ngIf="messageService.messages.length">

  <h2>提示信息</h2>
  <button class="clear" (click)="messageService.clear()">clear</button>
  <div *ngFor='let message of messageService.messages'> {{message}} </div>

</div>
```
* 美化视图 messages.component.css 拷贝官网教程上的

# 路由的使用
* 添加路由 `ng generate module app-routing --flat --module=app`
> --flat是让路由文件生成在 /src/app 下，而不是 /src/app/app-routing。（注意是--flat，我写了-flat给我创建了一个app-routing/目录）

> --module=app 是自动在 AppModule 的 imports[] 中注册路由，然后我基本可以断定：别在AppModule里面添加注释和排版，否则它自动添加的代码会出错，一段代码可能会跑到另外一段代码里面去。

* 看看路由的代码 src/app/app-routing.module.ts
```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
  imports: [
    CommonModule
  ],
  declarations: []
})

export class AppRoutingModule { }
```
* 官网教程说一般不会在路由模块中生命组件，所以可以不导入 CommonModule 并且可以删除 @NgModule.declarations（那你自动生成干嘛？），你需要从 @angular/router 中导入 RouterModule 和 Routes（那你干嘛不自动生成？），然后暴露RouterModule，处理后的代码是这样的
```
// 1导包
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from "@angular/router"; //导入RouterModule和Routes

// 2装饰器
@NgModule({
  exports: [
    RouterModule //暴露RouterModule以让AppModule可以使用它
  ]
})

// 3具体定义
export class AppRoutingModule { }

```
* 添加路由定义：1引入组件 2定义路由常量列表 3在装饰器中导入路由
```
...
// 导入组件
import { HeroesComponent } from './heroes/heroes.component';
// 路由定义
const routes: Routes = [
  // {path: 路由地址, component: 指向组件}
  {path: 'heroes', component: HeroesComponent},
];

// 2装饰器
@NgModule({
  imports: [
    RouterModule.forRoot(routes) //这里的routes是上面的常量routes，所以路由定义必须写在前面
  ],
  ...
})
...
```
* 更新“布局模板”，添加路由链接 a.routerLink 和路由出口 <router-outlet>
```
<h1>{{title}}</h1>
<nav>
    <!-- routerLink="路由地址" -->
    <a routerLink="/heroes"> 英雄列表 </a>
</nav>
<!-- <app-heroes></app-heroes>  现在不需要这句了 --> 
<!-- 将点击 -->
<router-outlet></router-outlet>
<app-messages></app-messages>
```

* 此时访问主页为一片空白，需要点击“英雄列表”才能看到相关内容

* 创建“仪表盘”组件（一个只显示4个最强英雄的图表） `ng g c dashboard`
  * 3要素：视图 dashboard.component.html
  ```
  <h3>Top Heroes</h3>
  <div class="grid grid-pad">
    <a *ngFor="let hero of heroes" class="col-1-4">
      <div class="module hero">
        <h4>{{hero.name}}</h4>
      </div>
    </a>
  </div>
  ```
  * 3要素：类定义 dashboard.component.ts
  ```
  import { Component, OnInit } from '@angular/core';
  // 导入 Hero 规范类， 导入 HeroService
  import { Hero } from "../hero";
  import { HeroService } from "../hero.service";

  @Component({
    selector: 'app-dashboard',
    templateUrl: './dashboard.component.html',
    styleUrls: ['./dashboard.component.css']
  })

  export class DashboardComponent implements OnInit {

    heroes: Hero[]; //定义变量 英雄列表

    // 构造函数帮我们实例化 HeroService
    constructor(private heroService:HeroService) { }

    ngOnInit() {
      this.getHeroes(); //初始化函数调用获取英雄数据方法
    }

    // getHeroes() 获取英雄数据
    getHeroes(): void {
      this.heroService.getHeroes()
        // 只获取前5个 
        .subscribe(heroes => this.heroes = heroes.slice(0, 4));
    }

  }
  ```
  * 3要素：样式 （官网copy，路由那一节有所有成型的组件需要的所有 css 全部拷贝）

* 添加仪表盘路由和默认路由 app-routing.module.ts
```
import { DashboardComponent } from "./dashboard/dashboard.component";

{ path: 'dashboard', component: DashboardComponent },
// 添加一条默认路由，即 localhost:4200 后面啥也没有的路由
{ path: '', redirectTo: '/dashboard', pathMatch: 'full' }
```
* 添加“仪表盘”路由链接到“布局模板”
```
...
<a routerLink="/dashboard"> 仪表盘 </a>
...
```

* 英雄详情： 现在再是在 “英雄列表”HeroesComponent下的一个小组件了，而是无论从“仪表盘”还是“英雄列表”中点击，都独立展示英雄详情的一块
  * 从HeroesComponent的模板中删除标签 `<app-hero-detail [hero]="selectedHero"></app-hero-detail>`
  * 给HeroService 添加一个方法 ： 根据id获取英雄详情 并将获取的数据对象作为返回值 
  ```
  // 3
  ...
  // 获取单个英雄的数据
  getHero(id: number): Observable<Hero> {
    this.messageService.add(`查看编号为${id}的英雄详情`); //添加提示信息
    return of(HEROES.find(hero => hero.id === id)); //根据id查询讯息 HEROES.find
  }
  ```
  * 3要素：模板 hero-detail.component.html
  ```
  <!-- 英雄详情 -->
  <!-- *ngIf="hero有值即为true" -->
  <div *ngIf="hero">

    <!-- 左边的值 | 调用右边的管道 uppercase全部大写 --> 
    <h2>{{ hero.name | uppercase }} Details</h2>
    <div><span>id: </span>{{hero.id}}</div>
    <div>
        <label>name:
          <!-- [(ngModel)]="被绑定的数据" -->
          <input [(ngModel)]="hero.name" placeholder="name">
        </label>
    </div>

    <!-- 添加一个返回的按钮 -->
    <button (click)="goBack()">返回上层</button>

  </div>
  ```
  * 3要素：类定义 hero-detail.component.ts
  ```
  // 1
  ...
  import { ActivatedRoute } from '@angular/router'; //该类主要处理url中参数
  import { Location } from '@angular/common'; //导入服务后才可以利用 它的通过id获得详情的函数 getHero(id)
  import { HeroService }  from '../hero.service'; //该类主要处理浏览器的功能实现

  // 3
  export class HeroDetailComponent implements OnInit {

    @Input() hero: Hero; //定义该属性

    constructor(
      // 依赖注入 实例化上面导入的类
      private route: ActivatedRoute,
      private heroService: HeroService,
      private location: Location
    ) { }

    ngOnInit(): void {
      // 初始化方法中 组件被激活则调用查询英雄数据的方法
      this.getHero();
    }
    
    getHero(): void {
      // 通过ActivatedRoute.snapshot.paramMap.get() 方法取得 参数id(英雄编号)
      const id = +this.route.snapshot.paramMap.get('id');
      this.heroService.getHero(id) //将id作为参数订阅英雄信息
        .subscribe(hero => this.hero = hero);
    }

    goBack(): void {
      // 通过 Location.back() 返回上一级页面
      this.location.back();
    }

  }
  ```

  * 创建路由 app-routing.module.ts
  ```
  import { HeroDetailComponent } from "./hero-detail/hero-detail.component";
  // 带参数的路由 path:'路由地址/:参数占位符'
  { path: 'detail/:id', component: HeroDetailComponent },
  ```

  * 在“英雄列表”和“仪表盘” 的视图上 给他们绑定链接，显示“英雄详情”
  ```
  # 英雄列表
  <!-- routerLink="地址/参数" -->
  <a routerLink="/detail/{{hero.id}}">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </a>

  #仪表盘
  <a *ngFor="let hero of heroes" class="col-1-4" routerLink="/detail/{{hero.id}}">
  ```

> 创建路由定义文件 `ng generate module app-routing --flat --module=app` ， --flat让该文件创建在 AppModule 同级目录下， --module=app 让 cli 帮我们完成路由在 AppModule 中的自动写入

> 路由定义文件应该是这样的
```
// 1
import { RouterModule, Routes } from "@angular/router"; //导入RouterModule和Routes

// 紧接着导入组件 和 配置路由列表
// 导入组件
import { HeroesComponent } from './heroes/heroes.component';
import { DashboardComponent } from "./dashboard/dashboard.component";
import { HeroDetailComponent } from "./hero-detail/hero-detail.component";
// 路由定义
const routes: Routes = [
  // {path: 路由地址, component: 指向组件}
  { path: 'heroes', component: HeroesComponent },
  { path: 'dashboard', component: DashboardComponent },
  // 带参数的路由 path:'路由地址/:参数占位符'
  { path: 'detail/:id', component: HeroDetailComponent },
  
  // 添加一条默认路由，即 localhost:4200 后面啥也没有的路由
  { path: '', redirectTo: '/dashboard', pathMatch: 'full' }
];

// 2装饰器中
// 2装饰器
@NgModule({
  imports: [
    RouterModule.forRoot(routes) //实现路由功能：这里的routes是上面的常量routes，所以路由定义必须写在前面
  ],
  exports: [
    RouterModule //暴露RouterModule以让AppModule可以使用它
  ]
})
```

> 在页面调用路由的时候：
```
# 在布局模板应该用路由出口标签
<router-outlet> </router-outlet>
# 路由的链接a标签的写法应该是
<a routerLink="路由地址/{{参数如果有的话}}">
```

# 单机版开发总结
> 为什么叫单机版： 因为我们的“英雄列表”是在本地建了一个hero.ts作规范，mock-heroes.ts根据规范模拟出来的数据。没有真正的和服务器沟通。

1. 组件  Component `ng g c 组件名`
  * 壳组件 AppComponent
    * 模板 html => 必须
    * 定义 ts => 必须
    * 样式 css => 可有可无
    * 测试 spec.ts => 可有可无
    * 壳组件就是“根组件”，它的模板就是“布局模板”，它的样式就是“通用样式”
  * 子组件 HeroesComponent / HeroDetailComponent / DashboardComponent / MessagesComponent => 前期子组件通过它们装饰器中.selector定义的标签的形式插入到根组建的模板中，后期我们使用路由后用< router-outlet> 路由出口标签实现载入组件。
  * 一个组件的定义通常分3部分
  ```
  // 1 ： 导入需要使用的"包"
  import { Something } from '包的相对路径';

  // 2 ： 装饰器，声明组件要渲染的标签，组件使用的模板，样式文件的地址
  @Component({
    selector: '标签',
    templateUrl: '模板',
    styleUrls: ['样式1', '样式2']
  })

  // 3 : 具体定义，默认自带构造函数，初始化函数，我们还可以在其中自定义变量和函数
  export class DashboardComponent implements OnInit {

    // 我们通常在 构造函数 中完成依赖注入
    constructor() { }

    ngOnInit() {
      // 我们通常在 初始化函数 中调用那些组件一旦载入就需要使用到的方法
    }

  }
  ```

2. 模块 Module 我们只有一个模型 app.module.ts
```
// 1导包
import { Something } from 'Somewhere';

// 2装饰器
@NgModule({
  // 使用的组件
  declarations: [],
  // 使用的其他模块
  imports: [],
  // 服务提供
  providers: [],
  // “引导组件”
  bootstrap: []
})

// 3类定义
export class AppModule { }
```

3. 模板 视图 即 组件的html 组件名.component.html
  * 模板插值 `{{变量名}}`
  * 路由导航 `<a routerLink="path"> 导航名字 </a>`
  * 路由出口 `<router-outlet></router-outlet>`
  * 载入子组件，标签名由 **子组件的装饰器** 定义  `<app-messages></app-messages>`
  * 遍历数据 `*ngFor="let 提取出来的单个对象 of 对象集合"`
  * 显示被遍历的数据的具体属性 `{{当前对象.属性}}`
  * 绑定点击事件 `(click)="函数名(参数列表)"` 事件触发后的函数写在组件的第3部分具体定义中。
  * if判断 `*ngIf="条件"` 写在标签的属性里，则当条件为true的时候，宿主标签才会在页面上显示。
  * 管道函数的调用 `{{ 参数 | 管道函数 }}` ，我们用过 uppercase ：将字符串的小写字母全部转换为大写。
  * 数据双向绑定
  ```
  # 在 模块中 导入 FormsModule
  import { FormsModule } from '@angular/forms';
  # 在 组件视图上绑定该数据 
  <input [(ngModel)]="hero.name" placeholder="name"> <!-- [(ngModel)]="对象.具体属性" -->
  ```

4. 组件与组件之间通讯（后面我们没用了）
  * 父组件定义了一个变量 selectedHero 和一个方法 onSelect() ，同时给所有被遍历得列表项绑定了点击事件，当他们被点击的时候给触发 onSelect() ，给 selectedHero 赋值。
  ```
  # 父组件模板中导入子组件
  <app-hero-detail [hero]="selectedHero"></app-hero-detail>
  <!-- [子组件的变量hero] = "父组件的变量selectedHero" -->
  ```
  * 子组件到如Input ， 并且定义变量 hero 的时候 加上 @Input() 修饰
  ```
  // 1导入Input
  import { Component, OnInit, Input } from '@angular/core';
  
  // 3定义变量时加上修饰
  @Input() hero: Hero;
  ```

5. 服务 Service `ng g s 服务名`
  * 创建服时直接注册该服务到模块 `ng g s 服务名 --module=模块名`
  * 如果没写 `--module=app` 的话，需要在AppModule中自己导入和注册
  ```
  // 1导包
  import { XxService } from "./xx.service"; 

  // 2装饰器中真正投入使用
  ...
  // 服务提供
  providers: [
    XxService, //让XxService真正投入使用
  ],
  ...
  ```
  * 服务定义也是 3部分
  ```
  // 1导包
  import { Injectable } from '@angular/core';

  // 2装饰器
  @Injectable({
    providedIn: 'root'
  })

  // 3具体定义
  export class HeroService {

    // 构造函数
    constructor() { }

  }
  ```
  * 服务主要作用是和后台沟通，处理数据，但是我们是用得本地的数据，所以实际上服务的作用没有体现出来
  * Observable 和 of() 的使用 （使数据的获取是异步的）
  ```
  // 1 服务定义里面导包 "rxjs"
  import { Observable, of } from "rxjs";  

  // 3 服务定义里面具体的用法
  函数(): Observable<返回值类型> {
    return of(返回的数据);
  }
  ```
  * 在组组件中使用服务：导入服务（确保已经在模块中注册），构造函数中用**依赖注入**的方法实例化服务。
  ```
  // 1 导入服务
  import { XxService }  from '../xx.service';

  // 3 构造函数中实例化服务
  constructor(
    // 依赖注入 实例化上面导入的类
    权限修饰符 属性: 导入的服务类;
    权限修饰符 属性: 导入的服务类;
    权限修饰符 属性: 导入的服务类;
  ) { }
  ```
  * 在 组件 中 **订阅subscribe()** 服务 提供的数据
  ```
  // 写一个方法调用
  函数名(参数列表): 返回值类型 {
    this.依赖注入时实例化的服务类.服务类中提供的数据获取方法()
      .subscribe(数据 => this.定义的变量 = 服务类方法提供的数据)
      .subscribe(hero => this.hero = hero); // 获取全部数据
      .subscribe(heroes => this.heroes = heroes.slice(0, 4)); //获取前5条
  } 
  ```
  * 在将函数写进 ngOnInit() 函数中以便在组件生成时候直接调用
  ```
  ngOnInit() {
    this.函数();
  }
  ```
6. 路由
  * 创建路由定义文件 `ng generate module app-routing --flat --module=app` 
    * --flat 是让该文件生成在 /src/app 下而不是单独新建一个文件
    * --module=app 是将该文件直接导入 AppModule 中
    ```
    // 1
    ...
    import { AppRoutingModule } from './/app-routing.module';

    // 2
    // 使用的其他模块
    imports: [
      BrowserModule,
      FormsModule,
      AppRoutingModule
    ],
    ```

  * 路由的定义 (其实我们是创建了一个模块)
  ```
  // 1导包
  import { NgModule } from '@angular/core';
  import { RouterModule, Routes } from "@angular/router"; //导入RouterModule和Routes

  // 导入组件
  import { HeroesComponent } from './heroes/heroes.component';
  import { DashboardComponent } from "./dashboard/dashboard.component";
  import { HeroDetailComponent } from "./hero-detail/hero-detail.component";
  
  // 路由定义
  const routes: Routes = [
    
    // {path: '路由地址', component: 指向组件}
    { path: 'heroes', component: HeroesComponent },
    { path: 'dashboard', component: DashboardComponent },
    
    // 带参数的路由 path:'路由地址/:参数占位符'
    { path: 'detail/:id', component: HeroDetailComponent },
    
    // 添加一条默认路由，即 localhost:4200 后面啥也没有的路由
    { path: '', redirectTo: '/dashboard', pathMatch: 'full' }
  ];

  // 2装饰器
  @NgModule({
    imports: [
      RouterModule.forRoot(routes) //这里的routes是上面的常量routes，所以路由定义必须写在前面
    ],
    exports: [
      RouterModule //暴露RouterModule以让AppModule可以使用它
    ]
  })

  // 3具体定义
  export class AppRoutingModule {}
  ```

  > 定义路由时 path: '不能有/'

  > 使用路由时 routerLink="/可以有而且官方教程上有但是我试过也可以没有"
