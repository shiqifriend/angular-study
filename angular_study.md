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

11.1.1 概述

表单的控制逻辑写在组件模板中，适合简单的表单类型。

11.1.2 快速上手

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

11.1.3 表单验证

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

11.2.1 概述

表单的控制逻辑写在组件类中，对验证逻辑拥有更多的控制权，适合复杂的表单的类型。

在模型驱动表单中，表单字段需要是 FormControl 类的实例，实例对象可以验证表单字段中的值，值是否被修改过等等

![img](https://cdn.nlark.com/yuque/0/2022/jpg/2320181/1667308740171-e7a92922-2f27-479e-a6ee-294d076f9fb3.jpg)

一组表单字段构成整个表单，整个表单需要是 FormGroup 类的实例，它可以对表单进行整体验证。

![img](https://cdn.nlark.com/yuque/0/2022/jpg/2320181/1667308740261-6f17d2b6-4e2c-4d59-84ef-d2739170a61a.jpg)

1. FormControl：表单组中的一个表单项
2. FormGroup：表单组，表单至少是一个 FormGroup
3. FormArray：用于复杂表单，可以动态添加表单项或表单组，在表单验证时，FormArray 中有一项没通过，整体没通过。

11.2.2 快速上手

1. 引入 ReactiveFormsModule

```javascript
import { ReactiveFormsModule } from "@angular/forms"
@NgModule({
  imports: [ReactiveFormsModule]
})
export class AppModule {}
```

1. 在组件类中创建 FormGroup 表单控制对象

```javascript
import { FormControl, FormGroup } from "@angular/forms"
export class AppComponent {
  contactForm: FormGroup = new FormGroup({
    name: new FormControl(),
    phone: new FormControl()
  })
}
```

1. 关联组件模板中的表单

```html
<form [formGroup]="contactForm" (submit)="onSubmit()">
  <input type="text" formControlName="name" />
  <input type="text" formControlName="phone" />
  <button>提交</button>
</form>
```

1. 获取表单值

```javascript
export class AppComponent {
  onSubmit() {
    console.log(this.contactForm.value)
  }
}
```

1. 设置表单默认值

```javascript
contactForm: FormGroup = new FormGroup({
  name: new FormControl("默认值"),
  phone: new FormControl(15888888888)
})
```

1. 表单分组

```javascript
contactForm: FormGroup = new FormGroup({
  fullName: new FormGroup({
    firstName: new FormControl(),
    lastName: new FormControl()
  }),
  phone: new FormControl()
})
<form [formGroup]="contactForm" (submit)="onSubmit()">
  <div formGroupName="fullName">
    <input type="text" formControlName="firstName" />
    <input type="text" formControlName="lastName" />
  </div>
  <input type="text" formControlName="phone" />
  <button>提交</button>
</form>
onSubmit() {
  console.log(this.contactForm.value.name.username)
  console.log(this.contactForm.get(["name", "username"])?.value)
}
```

11.2.3 FormArray

需求：在页面中默认显示一组联系方式，通过点击按钮可以添加更多联系方式组。

```javascript
import { Component, OnInit } from "@angular/core"
import { FormArray, FormControl, FormGroup } from "@angular/forms"
@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styles: []
})
export class AppComponent implements OnInit {
  // 表单
  contactForm: FormGroup = new FormGroup({
    contacts: new FormArray([])
  })
  get contacts() {
    return this.contactForm.get("contacts") as FormArray
  }
  // 添加联系方式
  addContact() {
    // 联系方式
    const myContact: FormGroup = new FormGroup({
      name: new FormControl(),
      address: new FormControl(),
      phone: new FormControl()
    })
    // 向联系方式数组中添加联系方式
    this.contacts.push(myContact)
  }
  // 删除联系方式
  removeContact(i: number) {
    this.contacts.removeAt(i)
  }
  ngOnInit() {
    // 添加默认的联系方式
    this.addContact()
  }
  onSubmit() {
    console.log(this.contactForm.value)
  }
}
<form [formGroup]="contactForm" (submit)="onSubmit()">
  <div formArrayName="contacts">
    <div
      *ngFor="let contact of contacts.controls; let i = index"
      [formGroupName]="i"
    >
      <input type="text" formControlName="name" />
      <input type="text" formControlName="address" />
      <input type="text" formControlName="phone" />
      <button (click)="removeContact(i)">删除联系方式</button>
    </div>
  </div>
  <button (click)="addContact()">添加联系方式</button>
  <button>提交</button>
</form>
```

11.2.4 内置表单验证器

1. 使用内置验证器提供的验证规则验证表单字段

```javascript
import { FormControl, FormGroup, Validators } from "@angular/forms"
contactForm: FormGroup = new FormGroup({
  name: new FormControl("默认值", [
    Validators.required,
    Validators.minLength(2)
  ])
})
```

1. 获取整体表单是否验证通过

```javascript
onSubmit() {
  console.log(this.contactForm.valid)
}
<!-- 表单整体未验证通过时禁用表单按钮 -->
<button [disabled]="contactForm.invalid">提交</button>
```

1. 在组件模板中显示为验证通过时的错误信息

```javascript
get name() {
  return this.contactForm.get("name")!
}
<form [formGroup]="contactForm" (submit)="onSubmit()">
  <input type="text" formControlName="name" />
  <div *ngIf="name.touched && name.invalid && name.errors">
    <div *ngIf="name.errors.required">请填写姓名</div>
    <div *ngIf="name.errors.maxlength">
      姓名长度不能大于
      {{ name.errors.maxlength.requiredLength }} 实际填写长度为
      {{ name.errors.maxlength.actualLength }}
    </div>
  </div>
</form>
```

11.2.5 自定义同步表单验证器

1. 自定义验证器的类型是 TypeScript 类
2. 类中包含具体的验证方法，验证方法必须为静态方法
3. 验证方法有一个参数 control，类型为 AbstractControl。其实就是 FormControl 类的实例对象的类型
4. 如果验证成功，返回 null
5. 如果验证失败，返回对象，对象中的属性即为验证标识，值为 true，标识该项验证失败
6. 验证方法的返回值为 ValidationErrors | null

```javascript
import { AbstractControl, ValidationErrors } from "@angular/forms"
export class NameValidators {
  // 字段值中不能包含空格
  static cannotContainSpace(control: AbstractControl): ValidationErrors | null {
    // 验证未通过
    if (/\s/.test(control.value)) return { cannotContainSpace: true }
    // 验证通过
    return null
  }
}
import { NameValidators } from "./Name.validators"
contactForm: FormGroup = new FormGroup({
  name: new FormControl("", [
    Validators.required,
    NameValidators.cannotContainSpace
  ])
})
<div *ngIf="name.touched && name.invalid && name.errors">
	<div *ngIf="name.errors.cannotContainSpace">姓名中不能包含空格</div>
</div>
```

11.2.6 自定义异步表单验证器

```javascript
import { AbstractControl, ValidationErrors } from "@angular/forms"
import { Observable } from "rxjs"
export class NameValidators {
  static shouldBeUnique(control: AbstractControl): Promise<ValidationErrors | null> {
    return new Promise(resolve => {
      if (control.value == "admin") {
         resolve({ shouldBeUnique: true })
       } else {
         resolve(null)
       }
    })
  }
}
contactForm: FormGroup = new FormGroup({
    name: new FormControl(
      "",
      [
        Validators.required
      ],
      NameValidators.shouldBeUnique
    )
  })
<div *ngIf="name.touched && name.invalid && name.errors">
  <div *ngIf="name.errors.shouldBeUnique">用户名重复</div>
</div>
<div *ngIf="name.pending">正在检测姓名是否重复</div>
```

11.2.7 FormBuilder

创建表单的快捷方式。

1. `this.fb.control`：表单项
2. `this.fb.group`：表单组，表单至少是一个 FormGroup
3. `this.fb.array`：用于复杂表单，可以动态添加表单项或表单组，在表单验证时，FormArray 中有一项没通过，整体没通过。

```javascript
import { FormBuilder, FormGroup, Validators } from "@angular/forms"
export class AppComponent {
  contactForm: FormGroup
  constructor(private fb: FormBuilder) {
    this.contactForm = this.fb.group({
      fullName: this.fb.group({
        firstName: ["😝", [Validators.required]],
        lastName: [""]
      }),
      phone: []
    })
  }
}
```

11.2.8 练习

1. 获取一组复选框中选中的值

```html
<form [formGroup]="form" (submit)="onSubmit()">
  <label *ngFor="let item of Data">
    <input type="checkbox" [value]="item.value" (change)="onChange($event)" />
    {{ item.name }}
  </label>
  <button>提交</button>
</form>
import { Component } from "@angular/core"
import { FormArray, FormBuilder, FormGroup } from "@angular/forms"
interface Data {
  name: string
  value: string
}
@Component({
  selector: "app-checkbox",
  templateUrl: "./checkbox.component.html",
  styles: []
})
export class CheckboxComponent {
  Data: Array<Data> = [
    { name: "Pear", value: "pear" },
    { name: "Plum", value: "plum" },
    { name: "Kiwi", value: "kiwi" },
    { name: "Apple", value: "apple" },
    { name: "Lime", value: "lime" }
  ]
  form: FormGroup
  constructor(private fb: FormBuilder) {
    this.form = this.fb.group({
      checkArray: this.fb.array([])
    })
  }
  onChange(event: Event) {
    const target = event.target as HTMLInputElement
    const checked = target.checked
    const value = target.value
    const checkArray = this.form.get("checkArray") as FormArray
    if (checked) {
      checkArray.push(this.fb.control(value))
    } else {
      const index = checkArray.controls.findIndex(
        control => control.value === value
      )
      checkArray.removeAt(index)
    }
  }
  onSubmit() {
    console.log(this.form.value)
  }
}
```

1. 获取单选框中选中的值

```javascript
export class AppComponent {
  form: FormGroup
  constructor(public fb: FormBuilder) {
    this.form = this.fb.group({ gender: "" })
  }
  onSubmit() {
    console.log(this.form.value)
  }
}
<form [formGroup]="form" (submit)="onSubmit()">
  <input type="radio" value="male" formControlName="gender" /> Male
  <input type="radio" value="female" formControlName="gender" /> Female
  <button type="submit">Submit</button>
</form>
```

11.2.9 其他

1. patchValue：设置表单控件的值（可以设置全部，也可以设置其中某一个，其他不受影响）
2. setValue：设置表单控件的值 (设置全部，不能排除任何一个)
3. valueChanges：当表单控件的值发生变化时被触发的事件
4. reset：表单内容置空

### 路由

```html
//query参数写法
<a routerLink="/about" [queryParams]="{ name: 'kitty' }">关于我们</a>
//parmas参数写法
<a [routerLink]="['/about', 'zhangsan']">关于我们</a>
```

#### 导航路由

```typescript
// app.component.ts
import { Router } from "@angular/router"
export class HomeComponent {
  constructor(private router: Router) {}
  jump() {
    this.router.navigate(["/about/history"], {
      queryParams: {
        name: "Kitty"
      }
    })
  }
}
```

#### 路由守卫

路由守卫会告诉路由是否允许导航到请求的路由。

路由守方法可以返回 boolean 或 Observable <boolean> 或 Promise <boolean>，它们在将来的某个时间点解析为布尔值。

12.10.1 CanActivate

检查用户是否可以访问某一个路由。

CanActivate 为接口，路由守卫类要实现该接口，该接口规定类中需要有 canActivate 方法，方法决定是否允许访问目标路由。

路由可以应用多个守卫，所有守卫方法都允许，路由才被允许访问，有一个守卫方法不允许，则路由不允许被访问。

创建路由守卫：`ng g guard guards/auth`

12.10.2 CanActivateChild

检查用户是否方可访问某个子路由。

创建路由守卫：`ng g guard guards/admin` 注意：选择 CanActivateChild，需要将箭头移动到这个选项并且敲击空格确认选择。

12.10.3 CanDeactivate

检查用户是否可以退出路由。比如用户在表单中输入的内容没有保存，用户又要离开路由，此时可以调用该守卫提示用户。

12.10.4 Resolve (路由数据预加载)

允许在进入路由之前先获取数据，待数据获取完成之后再进入路由。

```
ng g resolver <name>
```

### Rxjs

 快速入门 

1可观察对象  ( Observable ) ：类比 Promise 对象，内部可以用于执行异步代码，通过调用内部提供的方法将异步代码执行的结果传递到可观察对象外部。

2观察者 ( Observer )：类比 then 方法中的回调函数，用于接收可观察对象中传递出来数据。

3

订阅 ( subscribe )：类比 then 方法，通过订阅将可观察对象和观察者连接起来，当可观察对象发出数据时，订阅者可以接收到数据。

![img](https://cdn.nlark.com/yuque/0/2022/png/2320181/1667308740458-25dd1d2b-5777-4a73-be00-b725abcec104.png?x-oss-process=image%2Fresize%2Cw_337%2Climit_0)

```typescript
import { Observable } from "rxjs"
const observable = new Observable(function (observer) {
  setTimeout(function () {
    observer.next({
      name: "张三"
    })
  }, 2000)
})
const observer = {
  next: function (value) {
    console.log(value)
  }
}
observable.subscribe(observer)
```

1. 可观察对象是惰性的，只有被订阅后才会执行

2. 可观察对象可以有 n 多订阅者，每次被订阅时都会得到执行

![img](https://cdn.nlark.com/yuque/0/2022/png/2320181/1667308740531-b4033ab0-8cf2-44b7-a6c9-f8a121adbbef.png?x-oss-process=image%2Fresize%2Cw_340%2Climit_0)

取消订阅

```typescript
import { interval } from "rxjs"

const obs = interval(1000)

const subscription = obs.subscribe(console.log)

setTimeout(function () {

  subscription.unsubscribe()

}, 2000)
```

#### 13.4 操作符

1. 数据流：从可观察对象内部输出的数据就是数据流，可观察对象内部可以向外部源源不断的输出数据。
2. 操作符：用于操作数据流，可以对象数据流进行转换，过滤等等操作。

### ngRx

```typescript
import { Injectable } from "@angular/core"
import { Actions, createEffect, ofType } from "@ngrx/effects"
import { increment, increment_async } from "../actions/counter.actions"
import { mergeMap, map } from "rxjs/operators"
import { timer } from "rxjs"
// createEffect
// 用于创建 Effect, Effect 用于执行副作用.
// 调用方法时传递回调函数, 回调函数中返回 Observable 对象, 对象中要发出副作用执行完成后要触发的 Action 对象
// 回调函数的返回值在 createEffect 方法内部被继续返回, 最终返回值被存储在了 Effect 类的属性中
// NgRx 在实例化 Effect 类后, 会订阅 Effect 类属性, 当副作用执行完成后它会获取到要触发的 Action 对象并触发这个 Action
// Actions
// 当组件触发 Action 时, Effect 需要通过 Actions 服务接收 Action, 所以在 Effect 类中通过 constructor 构造函数参数的方式将 Actions 服务类的实例对象注入到 Effect 类中
// Actions 服务类的实例对象为 Observable 对象, 当有 Action 被触发时, Action 对象本身会作为数据流被发出
// ofType
// 对目标 Action 对象进行过滤.
// 参数为目标 Action 的 Action Creator 函数
// 如果未过滤出目标 Action 对象, 本次不会继续发送数据流
// 如果过滤出目标 Action 对象, 会将 Action 对象作为数据流继续发出
@Injectable()
export class CounterEffects {
  constructor(private actions: Actions) {
    // this.loadCount.subscribe(console.log)
  }
  loadCount = createEffect(() => {
    return this.actions.pipe(
      ofType(increment_async),
      mergeMap(() => timer(1000).pipe(map(() => increment({ count: 10 }))))
    )
  })
}
```

