title: 前端自动化构建之gulp篇
date: 2016-01-29 11:32:03
tags:
	- 前端
	- gulp
	- node
---
在前端自动化构建中最重要的部分就是gulp/grunt诸如此类的自动化构建工具。当前gulp正是如日中天，流式处理大大的提高了构建效率，诸多插件让gulp如虎添翼。
<!-- more -->
### gulp基础
gulp的安装与初步使用可以参考下列博文：
 - [gulp详细入门教程](http://www.ydcss.com/archives/1)
 - [前端构建工具gulp入门教程](http://segmentfault.com/a/1190000000372547)

### gulp常用插件介绍

#### gulp-concat
合并js文件

#### gulp-uglify
压缩js文件

#### [gulp-rename](https://github.com/hparra/gulp-rename)
重命名文件

#### [gulp-jshint](https://github.com/spalger/gulp-jshint)
jshint是一个强大的javascript代码检测工具，不仅可以帮助我们检测到可能的代码错误，也能帮助我们有效的避免编码的错误。
> JSHint默认使用用户home目录下的.jshintrc文件（json格式）作为配置文件,[配置说明](http://jshint.com/docs/options/)

以下是部分配置属性：
<!-- http://www.xiabingbao.com/gulp/2015/10/15/gulp-jshint/ -->

| prop          | description                                       |
| ------------- |:-------------                                     |
| bitwise	    | 如果是true，则禁止使用位运算符                      |
| curly         | 如果是true，则要求在if/while的模块时使用TAB结构      |
| debug         | 如果是true，则允许使用debugger的语句                |
| eqeqeq        | 如果是true，则要求在所有的比较时使用===和!==         |
| evil          | 如果是true，则允许使用eval方法                      |
| forin	        | 如果是true，则不允许for in在没有hasOwnProperty时使用 |
| maxerr        |默认是50。 表示多少错误时，jsLint停止分析代码          |
| newcap        | 如果是true，则构造函数必须大写                       |
| nopram	    | 如果是true，则不允许使用arguments.caller和arguments.callee|
| nomen         |  如果是true，则不允许在名称首部和尾部加下划线         |
| onevar        | 如果是true，则在一个函数中只能出现一次var        |
| passfail	    | 如果是true，则在遇到第一个错误的时候就终止         |
| plusplus      | 如果是true，则不允许使用++或者- -的操作           |
| regexp        | 如果是true，则正则中不允许使用.或者[^…]          |
| undef	        | 如果是ture，则所有的局部变量必须先声明之后才能使用          |
| sub           | 如果是true，则允许使用各种写法获取属性(一般使用.来获取一个对象的属性值)         |
| strict        | 如果是true，则需要使用strict的用法,详见http://ejohn.org/blog/ecmascript-5-strict-mode-json-and-more/         |
| white	        | 如果是true，则需要严格使用空格用法。         |

另外有一款和他搭配的插件能够更友好的显示错误提示jshint-stylish；使用方法如下（前提是安装了gulp、gulp-jshint、jshint-stylish插件）
```
var gulp = require('gulp');
var jshint = require('gulp-jshint');

gulp.task('lint', function() {
    gulp.src('src/app/*/*.js')
        .pipe(jshint())
        .pipe(jshint.reporter('jshint-stylish'));
});
```
#### [gulp-imagemin](https://github.com/sindresorhus/gulp-imagemin)
imagemin是用来压缩图片的插件

以下是示例代码（前提是安装了gulp、gulp-imagemin、imagemin-pngquant插件）
```
var gulp = require('gulp');
var imagemin = require('gulp-imagemin');
var pngquant = require('imagemin-pngquant');

gulp.task('img',function() {
    gulp.src('src/assets/img/*')
        .pipe(imagemin({
            progressive: true,
            svgoPlugins: [{removeViewBox: false}],
            use: [pngquant()]
        }))
        .pipe(gulp.dest('dist'));
})
```
#### del
删除文件或文件夹
示例代码：
```
var gulp = require('gulp');
var del = require('del');

gulp.task('clean', function(cb) {
    del(['./dist'], cb)
});

```
#### glup-sass
css预处理器less也有类似的插件，这里只介绍gulp-sass

> 未完
