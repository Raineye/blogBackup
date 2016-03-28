title: Ionic2文档整理
date: 2016-03-28 17:22:52
tags:
  - ionic2
---

ionic2.0@beta版本文档整理

<!-- more -->

# ionic 2.0文档整理
## ActionSheet
ActionSheet是一个对话框，让用户选择一个选项。而且用户必须要选择其中一个选项才能恢复与应用程序的交互（点击背景会执行`cancle`的事件）。当然也可以利用背景或者后退键来取消对话框从而恢复和程序的交互。

ActionSheet会从一个数组创建它的按钮选项。每一个按钮都拥有一些属性，例如`text` `handler` `role` 等等。如果`handler`返回`false`时，ActionSheet将不会被销毁。ActionSheet还可以选择有一个标题和副标题。

如果有一个button的`role`被设置为`cancel`那么这个按钮不管位于按钮数组的哪个位置它都会位于底部。ionic官方建议`destructive`类型的按钮最好位于数组的第一个位置。另外，如果ActionSheet是由于点击背景而被取消的，那么它将会执行和`cancle`类型的按钮点击一样的事件。

注意：如果有两个`cancle`类型的按钮，那么最后一个`cancel`类型的按钮会覆盖掉前面所有的`cancel`类型的按钮。

在创建ActionSheet的第一个参数中，你可以将所有的选项传递进去：`ActionSheet.create(opts)。

```TypeScript
  openMenu(){
    this.actionSheet = ActionSheet.create({
      title: 'Albums',
      buttons: [
         {
           text: 'Destructive',
           role: 'destructive',
           handler: () => {
             console.log('Destructive clicked');
           }
         },
         {
           text: 'Archive',
           handler: () => {
             console.log('Archive clicked');
           }
         },
         {
           text: 'Cancel',
           role: 'cancel',
           handler: () => {
             console.log('Cancel clicked');
           }
         }
       ]
    })
    this.nav.present(this.actionSheet);

  }
```

### 静态方法

#### create(opts)

- title `string`

	ActionSheet的标题

- subTitle `string`

	ActionSheet的副标题

- cssClass `string`

	自定义样式的css类

- enableBackdropDismiss `boolean`

	用户点击背景是否关闭ActionSheet

- buttons `array<any>`

	显示的按钮的数组

**按钮的属性**

- text `string`

	按钮上显示的文字

- icon `icon`

	按钮上显示的图标

- handler `any`

	点击后执行的函数

- cssClass `stirng`

- role `string`

	如何显示按钮，`destructive`或者`cancel`。如果没有设置此选项，那么将显示默认的按钮。

### 实例方法

#### `setTitle(title)`

- title `stirng`

	设置ActionSheet的标题

#### `setSubTitle(subTitle)

- title `stirng`

	设置ActionSheet的子标题

#### `addButton(button)`

- button `object`

	ActionSheet的按钮。


## Alert

Alert是一个对话框，可以向用户提供信息或者收集用户输入的信息。同样用户必须点击某个按钮才能销毁这个对话框。

和ActionSheet十分相似的是，点击`role`被设置成`cancle`的按钮和点击背景所触发的事件是一样的。

```
constructor(nav: NavController) {
  this.nav = nav;
}

presentAlert() {
  let alert = Alert.create({
    title: 'Low battery',
    subTitle: '10% of battery remaining',
    buttons: ['Dismiss']
  });
  this.nav.present(alert);
}

presentConfirm() {
  let alert = Alert.create({
    title: 'Confirm purchase',
    message: 'Do you want to buy this book?',
    buttons: [
      {
        text: 'Cancel',
        role: 'cancel',
        handler: () => {
          console.log('Cancel clicked');
        }
      },
      {
        text: 'Buy',
        handler: () => {
          console.log('Buy clicked');
        }
      }
    ]
  });
  this.nav.present(alert);
}

presentPrompt() {
  let alert = Alert.create({
    title: 'Login',
    inputs: [
      {
        name: 'username',
        placeholder: 'Username'
      },
      {
        name: 'password',
        placeholder: 'Password',
        type: 'password'
      }
    ],
    buttons: [
      {
        text: 'Cancel',
        role: 'cancel',
        handler: data => {
          console.log('Cancel clicked');
        }
      },
      {
        text: 'Login',
        handler: data => {
          if (User.isValid(data.username, data.password)) {
            // logged in!
          } else {
            // invalid login
            return false;
          }
        }
      }
    ]
  });
  this.nav.present(alert);
}
```

### 静态方法

#### `creat(opts)`

Alert选项

- title `string`
- subTitle `string`
- message `string`

	Alert显示的信息

- cssClass `string`
- inputs `array`

	Alert显示的输入框数组

- buttons `array`
- enableBackdropDismiss `boolean`

	点击背景是否销毁Alert

Input选项

- type `string`

	input的类型，例如：text、tel、number等等

- name `string`
- placeHolder `string`

	input的占位符，未输入时的提示信息。

- value `string`

	checkbox的值

- label `string`

	checkbox显示的文字

- checked `boolean`

	是否选中

- id `string`

	input的标识

Button的选项

- text `string`
- handler `any`
- cssClass `string`
- role `string`
	`null`或者`cancel`

### 实例方法

#### `setTitle(title)`

- title `stirng`

#### `setSubTitle(subTitle)`

#### `setMessage(message)`

#### `addButton(button)`

#### `setCssClass(cssClass)`

- cssClass `string`

	添加css class 到alert的外层。

## App

app是一个ionic的装饰器，它可以启动应用。通过一系列的参数作为应用程序的全局配置变量。`@App`可以接受一个模板属性或者一个模板地址。

```
import {App} from 'ionic-angular';

@App({
  templateUrl: 'app/app.html',
  providers: [DataService]
})

export class MyApp{
  // Anything we would want to do at the root of our app
}
```

### 属性

- config `object`

	app的配置信息

- prodMode `boolean`

	激活Angular的生产模式，在框架内关闭断言和其他检查。默认是`false`。

- pipes `array`

- providers `array`
- template `string`
- templateUrl `stirng`

## Badges

Badges是一种小部件，通常用于数值显示。

```
<ion-item>
  <ion-icon name="logo-twitter" item-left></ion-icon>
  Followers
  <ion-badge item-right>260k</ion-badge>
</ion-item>
```

Badges也可以给与颜色属性`<ion-badge secondary></ion-badge>
`

## Buttons
Button是ionic中的简单句子，可以显示文本、图标或者都显示。

### 属性

- outline

	带有边框的透明按钮

- clear

	不带边框的透明按钮

- round

	大圆角的按钮

- block

	一个填充其父容器的小圆角按钮

- full

	填充其父容器的直角按钮

- small

	一个小尺寸按钮

- large

	一个大尺寸按钮

- disabled

	一个不能点击的按钮

- fab

	一个浮动的按钮

- fab-left/fab-right/fab-center/fab-top/fab-bottom

	控制浮动按钮的位置

- color

	动态设置按钮的颜色属性。

- start\end

	在`<ion-navbar>`中的位置控制

### Icon Buttons

```
<!-- Float the icon left -->
<button>
  <ion-icon name="home"></ion-icon>
  Left Icon
</button>

<!-- Float the icon right -->
<button>
  Right Icon
  <ion-icon name="home"></ion-icon>
</button>

<!-- Only icon (no text) -->
<button>
  <ion-icon name="home"></ion-icon>
</button>
```

## Cards
Cards是一个css组件

### Card的组成

就像一个普通的页面一样，cards拥有一个头部和内容。

```
<ion-card>
  <ion-card-header>
    Header
  </ion-card-header>

  <img src="img/nin-live.png"/>

  <ion-card-content>
    The British use the term "header", but the American term "head-shot" the English simply refuse to adopt.
  </ion-card-content>
</ion-card>
```
## Checkbox

复选框拥有一个布尔值来标记自己是否被选中，使用`checked`可以来默认选中复选框，使用`disabled`来禁用该复选框。

```
<ion-item>
  <ion-label>Daenerys Targaryen</ion-label>
  <ion-checkbox dark checked="true"></ion-checkbox>
</ion-item>

<ion-item>
  <ion-label>Arya Stark</ion-label>
  <ion-checkbox disabled="true"></ion-checkbox>
</ion-item>

<ion-item>
	<ion-label>Jon Snow</ion-label>
   	<ion-checkbox [(ngModel)]="sausage"></ion-checkbox>
</ion-item>
```

### 特性

- checked `boolean`

	复选框是否被选中

- disabled `boolean`

	复选框是否禁用

### 事件

- change

	当复选框状态变化时触发

## Config
用来配置整个应用程序。

### 属性
- activator

	ios:highlight
	md:ripple


- actionSheetEnter

	ios:action-sheet-slide-in
	md:action-sheet-md-slide-in

- actionSheetLeave

	ios:action-sheet-slide-out
	md:action-sheet-md-slide-out

- alertEnter

	ios:alert-pop-in
	md:alert-md-pop-in

- alertLeave

	ios:alert-pop-out
	md:alert-md-pop-out

- backButtonText

	ios:Back

- backButtonIcon

	ios:ion-ios-arrow-back
	md:ion-md-arrow-back

- iconMode

	ios:ios
	md:md

- menuType

	ios:reveal
	md:overlay

- modalEnter

	ios:modal-slide-in
	md:modal-md-slide-in

- modalLeave

	ios:modal-slide-out
	md:modal-md-slide-out

- pageTransition

	ios:ios-transition
	md:md-transition

- pageTransitionDelay

	ios:16
	md:120

- tabbarPlacement

	ios:bottom
	md:top

- tabbarHighlight		

	md:top

- tabbarLayout


- tabSubPages		

	md:true

### 实例方法

#### `get(key, fallbackValue)`

- key `string`

	config的键

- fallbackValue `any`

#### `getBoolean(key)`

#### `set(platform,key,value)`

- platform `string`

	"ios"或者"android"

## Content
Content组件提供了易于使用的方法来控制滚动，同时可以和其他组件配合实现下拉刷新和上拉加载的功能。

### 实例方法

#### `onScrollElementTransitionEnd()`

#### `scrollTo(x,y,duration,tolerance)`
滚动到具体的坐标

- x `number`

	x轴距离

- y `number`

	y轴距离

- duration `number`

	滚动动画的持续时间

- tolerance `TODO`


此函数执行完毕后，返回一个`Promise`

#### `scrollToTop()`

滚动到顶部，执行完毕后返回一个`Promise`

#### `getContentDimensions()`
获取content的尺寸

## Events
Events是一个发布订阅式的事件系统。

```
// first page (publish an event when a user is created)
function createUser(user) {
  console.log('User created!')
  events.publish('user:created', user);
}

// second page (listen for the user created event)
events.subscribe('user:created', (user) => {
  console.log('Welcome', user);
});

```

### 实例方法

#### `subscribe(topic,handler)`

订阅某个事件，当有该事件被发布时，执行handler。

- topic `string`

	订阅的主题

- handler `function`

#### `unsubscribe(topic, handler)`
取消订阅某个主题。handler不再接受该主题发布的事件。

如果handler成功移除，改函数返回`true`

#### `publish(topic,eventData)

将事件发布到给定的主题

## Grid

ionic基于flexbox制作了一套网格框架。

```
<ion-col width-10>This column will take 10% of space</ion-col>
```

列的百分比属性：

- width-10	10%
- width-20	20%
- width-25	25%
- width-33	33.3333%
- width-50	50%
- width-67	66.6666%
- width-75	75%
- width-80	80%
- width-90	90%

用`offset`属性来设置列偏移（例如：`offset-25`）

## HideWhen

HideWhen用来设置某个平台或者某个屏幕方向时显示的元素。

```
<div hideWhen="android">
 I am hidden on Android!
</div>

<div hideWhen="ios">
 I am hidden on iOS!
</div>

<div hideWhen="android,ios">
 I am hidden on Android and iOS!
</div>

<div hideWhen="portrait">
 I am hidden on Portrait!
</div>

<div hideWhen="landscape">
 I am hidden on Landscape!
</div>
```

## Icon
Icon会自动识别平台并使用该平台的设计Icon。

```
<!-- 自动的识别平台并使用该平台的icon -->
<ion-icon name="star"></ion-icon>

<!-- 手动设置ios和android平台的图标 -->
<ion-icon ios="ios-home" md="md-home"></ion-icon>

<!-- 总是使用同一个图标，无论什么平台 -->
<ion-icon name="ios-clock"></ion-icon>
<ion-icon name="logo-twitter"></ion-icon>

<!-- 可变的name属性,myIcon是 -->
<ion-icon [name]="myIcon"></ion-icon>
```

### 属性

- name `string`
- ios `string`
- md `string`
- isActive `bool`

	设置该图标是否是活跃的图标。一般会用在tabbar中来将选中的tab图标置为活跃。

## Id
Id是一个应用程序中元素的唯一标识，可以通过它来获取到元素从而进行访问。

### 使用

```
<ion-checkbox id="myCheckbox"></ion-checkbox>
```

```
constructor(app: IonicApp) {
  this.app = app
}

ngAfterViewInit() {
  var checkbox = this.app.getComponent("myCheckbox");
  if (checkbox.checked) {
    console.log('checkbox is checked');
  }
}
```

通过IonicApp服务来访问元素。

注意：不建议使用Id，因为不能保证注册组件所在的页面是否已经被销毁或者离开当前视图。

## InfiniteScroll

当用户滚动到页面底部时，可以通过InfiniteScroll执行一个动作。

可以用来实现上拉加载。

### 使用方法

```
<ion-content>

 <ion-list>
   <ion-item *ngFor="#i of items"></ion-item>
 </ion-list>

 <ion-infinite-scroll (infinite)="doInfinite($event)">
   <ion-infinite-scroll-content></ion-infinite-scroll-content>
 </ion-infinite-scroll>

</ion-content>
```

```
@Page({...})
export class NewsFeedPage {

  constructor() {
    this.items = [];
    for (var i = 0; i < 30; i++) {
      this.items.push( this.items.length );
    }
  }

  doInfinite(infiniteScroll) {
    console.log('Begin async operation');

    setTimeout(() => {
      for (var i = 0; i < 30; i++) {
        this.items.push( this.items.length );
      }

      console.log('Async operation has ended');
      infiniteScroll.complete();
    }, 500);
  }

}
```

### Infinite Scroll Content

```
<ion-content>

  <ion-infinite-scroll (infinite)="doInfinite($event)">
    <ion-infinite-scroll-content
      loadingSpinner="bubbles"
      loadingText="Loading more data...">
    </ion-infinite-scroll-content>
  </ion-infinite-scroll>

</ion-content>

```

### 实例方法

#### `state()`

#### `complete()`
调用该方法来表示加载已经完成。

#### `enable(shouldEnable)`
调用该方法可以设置InfiniteScroll是否处于激活状态。当shouldEnable是`false`时，InfiniteScroll将被禁用。
- shouldEnable `boolean`

### 属性

- threshold `string`

	设置滚动触发的阙值。例如距离底部还有10%的时候触发事件。

### 输出事件

- infinite

## Input
`ion-input`拥有很多文本类型，例如：`text` `password` `email` `number` `search` `tel` 和 `url`。

## IonicApp

### 实例方法

#### `setTitle(val)`
设置网页标题。

#### `isProd()`
返回是否生产模式。默认为`false`。可以在`@App`中的`config`中配置。

#### `isScrolling()`
返回布尔值。

#### `getComponent()`

获取给定键值的组件。

## Item
Item的使用有三种方法：

- 使用`<ion-item>`来创建一个不可点击文本。
- 使用 `<button ion-item>`。通常这个元素会有一个(click)处理程序
- 使用`<a ion-item>`当项目需要包含一个链接。

## ItemSliding
创建一个可滑动的Item。

```
<ion-list>
  <ion-item-sliding *ngFor="#item of items">
    <button ion-item (click)="itemTapped(item)">

    </button>
    <ion-item-options>
      <button (click)="favorite(item)">Favorite</button>
      <button (click)="share(item)">Share</button>
    </ion-item-options>
  </ion-item-sliding>
</ion-list>
```

## Keyboard
Keyboard允许你使用ionic键盘插件提供的键盘事件。

```
export class MyClass{
 constructor(keyboard: Keyboard){
   this.keyboard = keyboard;
 }
}
```

### 实例方法

- `isOpen()`

	检查键盘是否打开，返回一个boolean

- `onClose(callback)`

	当键盘被关闭时回调一个callback
	返回callback

- `close()`

	关闭键盘

## List
### 实例方法
#### `enableSlidingItems(shouldEnable)`
是否开启滑动。

#### closeSlidingItems()
关闭滑动作
