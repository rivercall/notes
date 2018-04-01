### angular-cli构建项目

#### 1. 前置准备

```
node 和 npm 是否安装？  需要更新至一定版本 (验证： node -v; npm -v)
```

#### 2.  安装angular-cli

```
1. 全局安装 $ npm install -g @angular/cli (验证：$ ng version)
```

#### 3. 生成一个新项目

```
1. $ ng new my-app 或 $ ng new my-app --routing; --routing 为参数，还有很多其它的参数
```

#### 4. 运行该项目

```
1. $ cd my-app
2. $ ng serve / $ npm run start / $ npm start
	Angular CLI 从 .angular-cli.json 文件中加载配置信息
	Angular CLI 运行 Webpack 打包相关 JavaScript 和 CSS 文件
	Angular CLI 启动 webpack dev server 本地开发服务器，默认的地址是 localhost:4200
3. 如果报错，需要 npm install 下载各种包
```

#### 5. 安装第三方插件

```
1. npm install jquery -S            第三方插件    (生产环境Dependencies)
2. npm install @types/jquery -D     类型描述文件  (开发环境DevDependencies)
3. angular-cli.json 文件 script/style对象中添加相应的路径
```

#### 6. 添加新功能

```
ng g class my-new-class: 新建 class
ng g component my-new-component: 新建组件
ng g directive my-new-directive: 新建指令
ng g enum my-new-enum: 新建枚举
ng g module my-new-module: 新建模块
ng g pipe my-new-pipe: 新建管道
ng g service my-new-service: 新建服务
```

#### 7. 路由部分

##### 7.1 常见的参数

```
需在app.module.ts文件中引入路由组件
  Routes: 路由配置，url和组件对应关系；RouteOutlet展示哪个组件；
  RouterOutlet: html中标记路由内容呈现位置 <router-outlet>
  Router: ??  一个对象，用于执行路由，可通过调用其navigate()和navigateByUrl()方法导航至特定路由
  routerLink: html中声明路由导航用的指令
  ActivatedRoute:当前激活的路由对象，保存当前路由的信息，包括路由地址、路由参数等
```

##### 7.2 Routes

```
在 app-routing.module.ts 文件中配置；请记得 import { A } from "./home/A"
{ path:"", redirectTo:'/home', pathMatch:"full"}
{ path:"login", component: A } 路由配置
{ path:"**", component: 404 } **指页面不存在；该条信息应放在最后
注意： path中不能用"/开头"
```

##### 7.3 RouteOutlet (插座)

```
<router-outlet></router-outlet>
```

##### 7.4 routerLink

```
<a [routerLink]="['/']">主页</a>
<a [routerLink]="['/login']">登陆页</a>
注意：此处需要 "/" 或者 "./"
```

##### 7.5 Router对象

```
在 app.component.ts 文件中配置； 需要 import { Router } from "@angular/router"
写在 constructor 构造函数中
export class AppComponent implements OnInit{
  constructor (private router:Router){ 	}  //此处固定写法，声明router
  toLoginPage(){
    this.router.navigate(["/login"]) //调用router的方法
  }
}
对应html文件： <button value="点击跳转至登陆页" (click)="toLoginPage()"><button>
```

##### 7.6 路由传递数据

###### 7.6.1 路由查询参数传递数据

```
发送数据： <a [routerLink]="['/product']" [queryParams]="[{id:1}]"></a>
获取数据： /product?id=1&name=2   使用 ActivatedRoute.queryParams[id] 来获取数据

举例：
export class ProductComponent implements OnInit{
  private: productId: number;
  constructor(private productInfo:ActivatedRoute){}
  ngOnInit(){
    this.productId = this.routeInfo.snapshot.queryParams['id'];    //获取传入的productId; snapshot参数快照；subscribe()参数订阅
  }
}
	
```

###### 7.6.2 路由路径中传递数据

	定义路由时 { path:"/product/:id", component:A};  对应url: /product/1;  ActivatedRouter.params[id]获取数据
	1. 修改路由配置中的path属性
	2. <a [routerLink]="['/product',2]"></a>   //routerLink的值是一个数组，第二个值可以传递参数
	
	举例：
	export class ProductComponent implements OnInit{
	  private: productId: number;
	  constructor(private productInfo:ActivatedRoute){}
	  ngOnInit(){
	  	this.routeInfo.subscribe(function(params:Params){
	      this.prodductId = this.routeInfo.params['id']
	  	})
	    //获取传入的productId; snapshot参数快照; subscribe()参数订阅 rxjs中的语法;
	  }
	}

###### 7.6.3 路由配置中传递数据

	{ path:"/product/:id", component:A, data:[{isAAA:true},{isBBB:false}]}
	data是一个数组，可以通过ActivatedRouter.data[0][isAAA]获取数据
##### 7.7 子路由

```
<a [routerLink]="['./xx']"></a> 

{ path:"home", component: HomeComponent,
	children:[
      { path:'xx', component:xxComponent },
      { paht:'yy', component:yyComponent }
	]
}
```

##### 7.8 辅助路由

```
html标签： <router-outlet name="fuzhu"></router-outlet>
js文件配置： {path:"xx", component:xxComponent, outlet:"fuzhu"}
导航部分： <a [routerLink]="['/home',{outlets:{fuzhu:"xx"}}]"></a> 
		 <a [routerLink]="[{outlets:{fuzhu:"xx"}}]"></a>
		 <a [routerLink]="[{outlets:{primary:"home", fuzhu:"xx"}}]"></a>
```

##### 7.9 路由守卫（路由操作限制）

```
一些场景：
1. 用户登录并拥有权限才能进入某些路由
2. 注册时所有表单填写完成才能进入下一个路由
3. 用户未保存离开路由时提醒用户
{path:"", component:A, children:[], canActivate:[LoginGuard, xx, bb], canDeactivate:[aa], resolve:{product:ProductResolve}}	
```

###### 7.9.1 CanActivate

```
决定是否能够导航到某路由
1. 编写路由守卫
  import {CanActivate} from "@angular/router";
  export class LoginGuard implements CanActivate {
    //根据 canActivate 方法返回的布尔值决定路由请求是否通过
    canActivate(){
      let logingg:boolean = Math.random()>0.5;
      return logingg
    }
  }
2. 路由配置中添加路由守卫
  {path:"", component:A, children:[], canActivate:[LoginGuard, xx, bb]}	 //数组格式，可以有多个路由守卫
3. 路由配置中引入
  import { LoginGuard } from "./.."
4. 路由配置中
  @ngModule{
    imports: [RouterModule.forRoot(routes)],
  	exports: [RouterModule],
  	providers:[LoginGuard]
  }
```

###### 7.9.2 CanDeactivate

```
从当前路由离开时的情况
1. 编写路由守卫
  import {CanDeactivate} from "@angular/router";
  import {LoginComponent} from "../login/login.component";
  export class LoginGuard implements CanDeactivate <LoginComponent> {     //此处添加泛型，即要守卫的组件类型，可直接用组件名代替
    //根据 canDeactivate 方法返回的布尔值决定路由请求是否通过
    canDeactivate ( component:LoginComponent ){
      return window.confirm("是否离开?")
    }
  }
```

###### 7.9.3 resolve

```
进入路由前获取到数据，进入路由后可以立刻展示
  import {Resolve, ActivatedRouteSnapshot, RouteStateSnapshot} from "@angular/router";
  import {Product} from "../product/product.component";
  import {Observable} from "rxjs"；
  
  @Injectable()     //装饰器注入router
  export class ProductResolve implements Resolve <Product> {     //此处添加泛型，即要守卫的组件类型，可直接用组件名代替
  	constructor(router:Router){}
    Resolve (route:ActivatedRouteSnapshot, state:RouteStateSnapshot):  Observable<Product> | Promise<Product> | Product{
      let productId:number = route.params['id'];  // 此处route作用与snapshot相同，因为snapshot类型是ActivatedRouteSnapshot
      if(productId==1){
        return new Product(1,'iphone');
      }else{
        this.route.navigate(['/home']);   //此处router需要正在构造函数中声明
      }
    }
  }
```

##### 7.10 手动生成路由模块

```
如果没有用 ng g new myRroject --routing, 那么需要在app.module.ts文件中手动生成路由模块；
1. import { Routes } from "@angular/router";
2. const routerConfig:Routes = [
	{path:'', ..}
   ]
3. @ngModule({
  	 imports:[
  		RouterModule.forRoot(routerConfig)
  	 ]
   })
```

##### 7.11 监听路由变化

```
在判断事件类型需要导入对应的事件类型，如：
import { Router, NavigationStart } from '@angular/router';

监听单一事件
this.router.events
  .filter((event) => event instanceof NavigationEnd)
  .subscribe((event:NavigationEnd) => {
    //do something
});

监听多个事件
constructor(router:Router) {
  router.events.subscribe(event:Event => {
    if(event instanceof NavigationStart) {
      //
    } else if(event instanceof NavigationEnd) {
      //
    } else if(event instanceof NavigationCancel) {
      //
    } else if(event instanceof NavigationError) {
      //
    } else if(event instanceof RoutesRecognized) {
      //
    }
  });
}
```

#### 8. 依赖注入

##### 8.1  结构和概念

```
@NgModule({
  declarations: [AppComponent, LoginComponent, ...],
  imports: [BrowserModule, HttpModule, AppRoutingModule, ...],
  providers: [ProService, ...]   // 另一种写法 providers: [{provide:ProService, useClass:haha}, ...]; provide和useclass值相同时可简写
})

@Injectable()   //只有写了这句代码，服务才能注入进来

@component   //@Injectable()的子类
export class LoginComponent{
  product:Product;
  // 找到ProService类型的token, 在 @NgModule 中找到该token对应的类, 本例为 haha, 实例化haha并注入到 productService 中;
  constructor(productService:ProService){   
  	this.product = this.productService.getProduct() // 调用getProduct方法
  }
}
```

##### 8.2 注入器和提供器

```
注入器:
  constructor(private productService:ProService ){ ... };
提供器:
  providers: [ProService, ...];  //provide和useclass值相同时可简写
  providers: [{provide:ProService, useClass:haha}, ...];
  providers: [{provide:ProService, useFactory()=>{ ... }}, ...];
  providers: [{provide:ProService, useValue:false}, ...];
```

##### 8.3 提供器作用域

```
1. @NgModule中全局可见
2. @component 只对该组件及其子组件可见
	@Component({
      selector: 'app-login',
      templateUrl: './login.component.html',
      styleUrls: ['./login.component.css'],
      providers:[]         //此处用于在组件中添加提供器
    })
3. 服务的提供器应优先声明在模块中 @NgModule
4. @component会覆盖 @NgModule中声明的提供器
```

#### 9. 数据操作部分

##### 9.1 数据绑定

```
<h1>{{titile}}</h1>; 
<img [src]="data.url">; 
<button (click)="toLoginPage()">跳转页面</button>;
<button (click)="saved=true">跳转页面</button>;
```

##### 9.2 属性绑定

```
1. 插值表达式和属性绑定是一个概念;
<img [src]="data.url"> 和 <img src="{{data.url}}"> 等价
```

##### 9.3 class属性

```
<div class="aaa"  [class]="bbb"></div>  //全替换式绑定,最终aaa消失了
<div class="a b"  [class.c]="isActive"></div>  //a和b固定，c类会根据isActive类进行改变
```

##### 9.4 双向绑定

```
<input [(ngModel)]='name'>;   ngModel适用于表单标签，不适用于div等标签
```

##### 9.5 一些语法格式

```
*ngIf; *ngFor="let item of items"; 
```

#### 10. 观察者模式Rxjs

##### 10.1 概念

```
var subscription = Observable.from([1,2,3,4])   // 一组值
.filter((e)=>e%2==0)
.map((e)=>e*e)
.subscribe(
    e=>console.log(e),                     // 处理事件
    error=>console.error(error),           // 处理异常
    ()=>console.log('结束啦')              // 结束时被调用
);

var button = document.querySelector('button')
Observable.fromEvent(button,'click') //一个事件

可观察对象Observable(流)，表示一组值或事件的集合；
观察者 Observer: 一个回调函数集合，知道如何监听被Observable发送的值；
订阅：subcription, 表示一个可观察对象，用于取消注册   // subscription.unsubscribe()
操作符： Operator, 纯粹的函数，可以通过函数编程方式处理集合 // filter()   map()
```

##### 10.2 如何进入观察者模式

```
loginComponent.ts文件中
import {Observable} from 'rxjs';
export class LoginComponent implement OnInit{
  constructor(){
    Observable.from([1,2,3,4])
  }
}
```

##### 10.3 一个例子

```
场景：input输入内容进行查询，500ms delay
代码：
@NgModule({
  imports:[ReactiveFormModule, ...]
})
export class LoginComponent{
  searchInput:FormControl = new FormControl()  //import {FormControl} form "@angular/forms"
}
<input [formControl]="searchInput">
constructor(){
  this.searchInput.valueChanges
  	.debounceTime(500)   // 需import "rxjs/Rx"
  	.subscribe(number => changeNumber(number))
}
changeNumber(num:number){ console.log(num)}
```

#### 11. 管道

##### 11.1 基本格式

```
{{birthday | date | uppercase}}
{{birthday | date:"yyyy-MM-dd hh-mm-ss" }}
{{3.14159 | number:"2.1-2"}}  //两位整数，1-2位小数
{{birthday | async }}   //异步管道，解包异步的可观测流
```

##### 11.2 自定义管道

```
ng g pipe pipe/ddd
@NgModule({
  declarations:[AddComponent, DddPipe, ...]
})
```

#### 12.组件间通信

##### 12.1 输入属性

```
场景：父元素输入数据，在子组件中显示  //单向输入，子组件无法对父组件造成影响
// html文件
  <div>
      <input type='text' [{ngModel}]='stockValue'>
      <app-children [stockCode]="stockValue"></app-children>
  </div>
// 父组件
  stockValue=""
//子组件
  import {Component, Input, OnInit} from "@angular/core"
  @input()
  stockCode: string;
```

##### 12.2 输出属性

```
// 子组件发射代码
  import {Component, Output, EventEmitter, OnInit} from "@angular/core"
  @Output()
  lastPrice:EventEmitter<PriceQuote> = new EventEmitter()
  this.lastPrice.emit(priceQuote)
  
// 父组件接收
  priceQuote:PriceQuote = new PriceQuote('', 0)
  priceQuotehandler(event:PriceQuote){
    this.priceQuote = event;
  }
// html文件
<div>
	<app-price-quote (lastPrice)="priceQuotehandler($event)"></app-price-quote>
	<div>这是父级显示区域{{priceQuote.stockCode}}</div>
</div>
```

##### 12.3 中间人模式

```
输入和输出属性的集合
```







- ​
- 无序列表：输入-之后输入空格
- 有序列表：输入数字+“.”之后输入空格
- [###个数 代表 h1-h6]
- 标题：ctrl+数字    *// h1&h2有下划线*
- 表格：ctrl+t
- 搜索：ctrl+f
- 替换：ctrl+h
- 代码块：ctrl+alt+f
- 加粗：ctrl+b
- 倾斜：ctrl+i
- 下划线：ctrl+u
- 删除线：alt+shift+5
- 插入图片：直接拖动到指定位置即可或者ctrl+shift+i
- 插入链接：ctrl+k