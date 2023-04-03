#### 1. main.ts:

```typescript
// enableProdMode 方法调用后将会开启生产模式
import { enableProdMode } from "@angular/core"
// Angular 应用程序的启动在不同的平台上是不一样的
// 在浏览器中启动时需要用到 platformBrowserDynamic 方法, 该方法返回平台实例对象
import { platformBrowserDynamic } from "@angular/platform-browser-dynamic"
// 引入根模块 用于启动应用程序
import { AppModule } from "./app/app.module"
// 引入环境变量对象 { production: false }
import { environment } from "./environments/environment"
// 如果当前为生产环境
if (environment.production) {
  // 开启生产模式
  enableProdMode()
}
// 启动应用程序
platformBrowserDynamic()
  .bootstrapModule(AppModule)
  .catch(err => console.error(err))
```

#### 2.app.module.ts

```typescript
// BrowserModule 提供了启动和运行浏览器应用所必需的服务
// CommonModule 提供各种服务和指令, 例如 ngIf 和 ngFor, 与平台无关
// BrowserModule 导入了 CommonModule, 又重新导出了 CommonModule, 使其所有指令都可用于导入 BrowserModule 的任何模块 
import { BrowserModule } from "@angular/platform-browser"
// NgModule: Angular 模块装饰器
import { NgModule } from "@angular/core"
// 根组件
import { AppComponent } from "./app.component"
// 调用 NgModule 装饰器, 告诉 Angular 当前类表示的是 Angular 模块
@NgModule({
  // 声明当前模块拥有哪些组件
  declarations: [AppComponent],
  // 声明当前模块依赖了哪些其他模块
  imports: [BrowserModule],
  // 声明服务的作用域, 数组中接收服务类, 表示该服务只能在当前模块的组件中使用
  providers: [],
  // 可引导组件, Angular 会在引导过程中把它加载到 DOM 中
  bootstrap: [AppComponent]
})
export class AppModule {}
```

#### 3.app.component.ts

```typescript
import { Component } from "@angular/core"
@Component({
  // 指定组件的使用方式, 当前为标记形式
  // app-home   =>  <app-home></app-home>
	// [app-home] =>  <div app-home></div>
  // .app-home  =>  <div class="app-home"></div>
  selector: "app-root",
  // 关联组件模板文件
  // templateUrl:'组件模板文件路径'
	// template:`组件模板字符串`
  templateUrl: "./app.component.html",
  // 关联组件样式文件
  // styleUrls : ['组件样式文件路径']
	// styles : [`组件样式`]
  styleUrls: ["./app.component.css"]
})
export class AppComponent {}
```

#### 双向数据绑定

```typescript
import { FormsModule } from "@angular/forms"
@NgModule({
  imports: [FormsModule],
})
export class AppModule {}
```

用法：

```html
<input type="text" [(ngModel)]="username" />
<button (click)="change()">在组件类中更改 username</button>
<div>username: {{ username }}</div>
```

#### 内容投影

ng-content在浏览器中会被 <div class="heading"></div> 替代，如果不想要这个额外的div，可以使用ng-container替代这个div。

#### 全局样式

```typescript
/* 第一种方式 在 styles.css 文件中 */
@import "~bootstrap/dist/css/bootstrap.css";
/* ~ 相对node_modules文件夹 */


<!-- 第二种方式 在 index.html 文件中  -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
 
  
  // 第三种方式 在 angular.json 文件中
"styles": [
  "./node_modules/bootstrap/dist/css/bootstrap.min.css",
  "src/styles.css"
]
```

#### 指令

属性指令：修改现有元素的外观或行为，使用 [] 包裹。

结构指令：增加、删除 DOM 节点以修改布局，使用*作为指令前缀

5.1.1 *ngIf

根据条件渲染 DOM 节点或**移除**DOM 节点。

5.1.2 [hidden]

根据条件显示 DOM 节点或隐藏 DOM 节点 (display)。

5.1.3 *ngFor

遍历数据生成HTML结构

在 Vue  和 React 中，都有一个 key 属性,能够提高效率，Angular 也有，那就是 `trackBy`，她接收一个函数，但不调用 `identify`是函数的名称，函数返回 item.id 作为key

```html
<li *ngFor="let item of list; trackBy: identify"></li>
```

```typescript
identify(index, item){
  return item.id; 
}
```

#### 生命周期

##### 1. 挂载阶段

挂载阶段的生命周期函数只在挂载阶段执行一次，数据更新时不再执行。

1. constructor
   Angular 在实例化组件类时执行,  可以用来接收 Angular 注入的服务实例对象。
2. ngOnInit
   在首次接收到输入属性值（@Input()...）后执行，在此处可以执行请求操作。
3. ngAfterContentInit
   当内容投影初始渲染完成后调用。
4. ngAfterViewInit
   当组件视图渲染完成后调用。

##### 2. 更新阶段

1. **ngOnChanges**

1. 1. 当输入属性值发生变化时执行，初始设置时也会执行一次，顺序优于 ngOnInit
   2. 不论多少输入属性同时变化，钩子函数只会执行一次，变化的值会同时存储在参数中
   3. 参数类型为 SimpleChanges，子属性类型为 SimpleChange
   4. 对于基本数据类型来说, 只要值发生变化就可以被检测到
   5. 对于引用数据类型来说, 可以检测从一个对象变成另一个对象, 但是检测不到同一个对象中属性值的变化，但是不影响组件模板更新数据。

2. ngDoCheck：主要用于调试，只要输入属性发生变化，不论是基本数据类型还是引用数据类型还是引用数据类型中的属性变化，都会执行。

3. ngAfterContentChecked：内容投影更新完成后执行。

4. ngAfterViewChecked：组件视图更新完成后执行。

##### 3. 卸载阶段

1. ngOnDestroy
   当组件被销毁之前调用, 用于清理操作。

#### 依赖注入

依赖注入 ( Dependency Injection ) 简称DI，是面向对象编程中的一种设计原则，用来减少代码之间的**耦合度**。

```javascript
class MailService {
  constructor(APIKEY) {}
}
class EmailSender {
  mailService: MailService
  constructor() {
    this.mailService = new MailService("APIKEY1234567890")
  }
  sendMail(mail) {
    this.mailService.sendMail(mail)
  }
}
const emailSender = new EmailSender()
emailSender.sendMail(mail)
```

EmailSender 类运行时要使用 MailService 类，EmailSender 类依赖 MailService 类，MailService 类是 EmailSender 类的依赖项。

以上写法的耦合度太高，代码并不健壮。如果 MailService 类改变了参数的传递方式，在 EmailSender 类中的写法也要跟着改变。

```javascript
class EmailSender {
  mailService: MailService
  constructor(mailService: MailService) {
    this.mailService = mailService;
  }
}
const mailService = new MailService("APIKEY1234567890")
const emailSender = new EmailSender(mailService)
```

在实例化 EmailSender 类时将它的依赖项通过 constructor 构造函数参数的形式注入到类的内部，这种写法就是依赖注入。

通过依赖注入降了代码之间的耦合度，增加了代码的可维护性。MailService 类中代码的更改再也不会影响 EmailSender 类。

####  DI 框架

Angular 有自己的 DI 框架，它将实现依赖注入的过程隐藏了，对于开发者来说只需使用很简单的代码就可以使用复杂的依赖注入功能。

在 Angular 的 DI 框架中有四个核心概念：

1. Dependency：组件要依赖的实例对象，服务实例对象
2. Token：获取服务实例对象的标识
3. Injector：注入器，负责创建维护服务类的实例对象并向组件中注入服务实例对象。
4. Provider：配置注入器的对象，指定创建服务实例对象的服务类和获取实例对象的标识。

### 表单

在 Angular 中，表单有两种类型，分别为模板驱动和模型驱动。

#### 11.1 模板驱动

##### 11.1.1 概述

表单的控制逻辑写在组件模板中，适合简单的表单类型。

##### 11.1.2 快速上手

1. 引入依赖模块 FormsModule

```javascript
import { FormsModule } from "@angular/forms"
@NgModule({
  imports: [FormsModule],
})
export class AppModule {}
```

1. 将 DOM 表单转换为 ngForm

```html
<form #f="ngForm" (submit)="onSubmit(f)"></form>
```

1. 声明表单字段为 ngModel

```html
<form #f="ngForm" (submit)="onSubmit(f)">
  <input type="text" name="username" ngModel />
  <button>提交</button>
</form>
```

1. 获取表单字段值

```javascript
import { NgForm } from "@angular/forms"
export class AppComponent {
  onSubmit(form: NgForm) {
    console.log(form.value)
  }
}
```

1. 表单分组

```html
<form #f="ngForm" (submit)="onSubmit(f)">
  <div ngModelGroup="user">
    <input type="text" name="username" ngModel />
  </div>
  <div ngModelGroup="contact">
    <input type="text" name="phone" ngModel />
  </div>
  <button>提交</button>
</form>
```

##### 11.1.3 表单验证

- required 必填字段
- minlength 字段最小长度
- maxlength 字段最大长度
- pattern 验证正则 例如：pattern="\d" 匹配一个数值

```html
<form #f="ngForm" (submit)="onSubmit(f)">
  <input type="text" name="username" ngModel required pattern="\d" />
  <button>提交</button>
</form>
export class AppComponent {
  onSubmit(form: NgForm) {
    // 查看表单整体是否验证通过
    console.log(form.valid)
  }
}
<!-- 表单整体未通过验证时禁用提交表单 -->
<button type="submit" [disabled]="f.invalid">提交</button>
```

在组件模板中显示表单项未通过时的错误信息。

```html
<form #f="ngForm" (submit)="onSubmit(f)">
  <input #username="ngModel" />
  <div *ngIf="username.touched && !username.valid && username.errors">
    <div *ngIf="username.errors.required">请填写用户名</div>
    <div *ngIf="username.errors.pattern">不符合正则规则</div>
  </div>
</form>
```

指定表单项未通过验证时的样式。

```css
input.ng-touched.ng-invalid {
  border: 2px solid red;
}
```

#### 11.2 模型驱动

##### 11.2.1 概述

表单的控制逻辑写在组件类中，对验证逻辑拥有更多的控制权，适合复杂的表单的类型。

在模型驱动表单中，表单字段需要是 FormControl 类的实例，实例对象可以验证表单字段中的值，值是否被修改过等等