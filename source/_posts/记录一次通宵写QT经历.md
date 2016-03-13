title: 记录一次通宵写QT经历
date: 2016-01-06 19:18:33
tags:
    - 杂记
---
因为当时专业选模块的时候脑子一热选了嵌入式，但是后来发现自己兴趣在前端，然后就发生了这样的惨案。通宵写课程大作业，一节课都没去上过。

<!-- more -->

### 学习QT
刚开始根本不知道如何入手，c++的知识老早忘的一干二净了。然后找了一些基础课程看了一下，对照着做了几个以后，开始规划大作业，大作业的要求很简单，就是要实现一个基于QT的点菜系统，数据库已经设计好了，拥有三张表，menu（菜单表）、orderMenu（餐桌&点菜）、orderInfo（点菜单表）。
* menu:DishId(菜的ID)、DishName(菜的名称)、Price(价格)...(剩余包括一些菜的英文名等等字段)
* orderMenu:TableNum(餐桌的的主键)、OrderId(点菜单ID)
* orderInfo:OrderId、DishId;
分为后台和前台，简单的说就是一下的功能
* 后台的功能是：对菜的增删查改和对已经开桌的餐桌进行删除操作
* 前台的功能是：选择对应餐桌Id后对点菜单进行增删查。
所以至少要有三个窗口来实现这个系统
* 欢迎页面（选择前台后台）
* 前台操作页面
* 后台操作页面

### 关于界面
在QT Creator中新建一个QT工程，包含一个设计师类，老师本来不推荐我们用这个东西，我为了为了布局可视化也不管了，直接用了，其实用设计师类实现和用代码实现是一样的，最终这个`*.ui`文件也会生成一个类用来管理界面，只不过设计师类会更加直观一点。
然后进入设计界面设计
![图片丢失了](http://7xpp66.com1.z0.glb.clouddn.com/mainWindow.jpg)
不要吐槽这个界面！！！
界面中使用了两个Layout，他们的层级关系是这样的：
![图片丢失了](http://7xpp66.com1.z0.glb.clouddn.com/mainLayout.png)

### 关于信号槽
在代码中使用`ui->objectName`可以调用ui界面中设计的控件，现在可以给按钮添加事件了。
首先在头文件中声明槽函数
```c++
public slots:
    void openEnd();
    void openselect();
```
然后在主文件中实现这个函数，我以`openEnd()`为例
```c++
void MainWindow::openEnd(){
    endWindow *w = new endWindow();
    w->show();
}
```
这里`endWindow`是我在工程中添加的一个新的`endWindow`类，可以右键点击你的工程目录->选择添加新文件->QT->设计师界面类然后记得勾选添加新UI。
现在槽函数声明好了，就要将谁发送信号和谁接受信号告诉计算机。
```c++
connect(ui->selectButton,SIGNAL(clicked()),this,SLOT(openselect()));
```
让我们来解析一下这四个参数
1.发送者
也就是这个信号是谁触发的
2.信号
是一个什么样的触发条件`clicked`即点击触发
3.接受者
这个信号谁来接受
4.槽
接受者要做些什么事情

### 关于QSqlTableModel
这是QT提供的一个动态链接数据库单表的类，它能够帮助我们很简单的实现对数据库的增删查改操作，而不用通过SQL去操纵数据库，对于这晚的我来说简直是一个完美的工具。
首先使用`QSqlTableModel`要让程序和一个数据库建立链接
```c++
bool createConnection()
{
    QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
    db.setDatabaseName("mydb.dat");
    if (!db.open()) {
        return false;
    }
    return true;
}
```
然后声明一个QSqlTableModel类的实例并关联数据库的一张表
```c++
QSqlTableModel *menuModel new QSqlTableModel(this);
menuModel = new QSqlTableModel(this);
menuModel->setTable("menu");//关联menu表
```
最后你可以把这个表的数据绑定到界面上一个tableview上
```c++
    menuModel->setEditStrategy(QSqlTableModel::OnManualSubmit);
    menuModel->select();
    ui->menuTableView->setModel(menuModel);
```
这样这张表已经完美的展现出来了

接着你会想要对这张表进行增删查改的操作，这里我推荐一篇介绍的很详细的博客[Qt数据库 QSqlTableModel实例操作](http://mobile.51cto.com/symbian-271567.htm)。

### 困难
* QT库
不太熟悉QT里面各种库的用法，然后去各种百度各种查文档，嗯，文档是英文。
* 槽函数
对于QT自有的信号槽机制查了很多资料，本来以为多么高大上的东西，看来是我想的太复杂了，本质其实就是一个信号传递的机制，由发起者发送信号，接受者接受信号然后接受者处理事件。

### 总结
对c++的理解更加深刻，对与以前常使用的Angular中的信号机制有了更深的理解。以前一直觉得网页前端布局很难，自从真正自己写了QT以后才明白，之前自己真是身在福中不知福。
