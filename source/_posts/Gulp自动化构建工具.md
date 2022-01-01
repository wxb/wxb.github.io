title: Gulp自动化构建工具
categories:
  - FE前端
tags:
  - 自动化
  - 学习笔记
  - 原创
toc: true
author: 虢國技酱
comments: true
date: 2016-10-17 17:42:31
description:
original:
permalink:
---

# Gulp 自动化构建工具   
![Gulp](https://github.com/wxb/FE/blob/master/gulp/gulp.png?raw=true)
看到这个“自动化构建工具”是不是有些似曾相识呢？ 对的，没错，大名鼎鼎的Grunt也是做这件事情的，当然百度Fis3也是，就相当于PHP中的各种框架，可能Gulp就像当下在PHP中的laravel吧。
<!-- more -->

* Gulp中文网 [Gulpjs.com](http://www.gulpjs.com.cn/)
* npm主页 [npm-gulp](https://www.npmjs.com/package/gulp)
* 前端工程的构建工具对比 [Grunt vs Gulp](http://segmentfault.com/a/1190000002491282)

# 终于明白了？

了解了这些我终于明白：原来这个php中五花八门的框架一样，都只是一种工具，可以帮助你做许多重复的、可以自动化的事情。想通这个你就不会像我一样：尼玛，一万个东西，到底有什么区别？根本想不通这些都是干什么的！    

现在终于知道了：原来这些东西就跟php中的框架一样，有很大框架，做的事情也是大同小异，只是各个框架之间有自己的一套设计理念。然后就是每个框架中都有针对完成某些功能的自己的类库，也就是那些开头以`grunt-***-***` 和 `gulp-***-***`。当然也不完全是这样，很多类库也是可以公用的！好了，下面记录一下自己学习Gulp

# Gulp 安装  

Gulp是基于nodejs的，所以首先需要对node有所了解，明白如何通过npm安装Gulp。如果对node完全不知道，那首先应该去简单的了解一下node。下面开始Gulp的安装！

 1. 全局安装 gulp：   
 `$ npm install --global gulp`

 2. npm初始化一个项目：demo   

     ```shell
     $ mkdir demo && cd demo  
     $ npm init   
     ```
     之后npm会提示你输入或确认一下应用信息：**应用名称（name），版本（version），描述（description），入口文件（entry point），测试命令（test command），git仓库地址（git repository），关键字（keywords），协议（license）**   

     ![npm init](https://github.com/wxb/FE/blob/master/gulp/npm%20init.png?raw=true)

 3. 作为项目的开发依赖（devDependencies）安装：    
 `$ npm install --save-dev gulp`       
 `--save` 和 `--save-dev`的区别：前者是说明发布正式应用的依赖信息，后者是开发依赖信息，一半情况下，如果我们只是网站开发，不会将我们的东西作为一个node的一个工具插件在npm上发布，所以我们只需要 `--save-dev`。    
 这一步安装只是在应用中安装gulp，所以会在packag.js文件中看到
 ![install gulp](https://github.com/wxb/FE/blob/master/gulp/install%20gulp.jpg?raw=true)
 同时，还会出现一个文件夹：node_modules，也就是gulp类库文件保存地址。

 4. 在项目根目录下创建一个名为 gulpfile.js 的文件：    

     ```js
     var gulp = require('gulp');     
     gulp.task('default', function() {     
     // 将你的默认的任务代码放在这    
     });
     ```
  ![gulpfile](https://github.com/wxb/FE/blob/master/gulp/gulpfile.jpg?raw=true)

 5. 运行 gulp：    
     `$ gulp`


# gulp 命令  

gulp 相对于grunt简单是因为：gulp构建简单，快速，易学，很少的API。了解grunt后在来看看gulp的配置简直就是so easy 啊！很多人在学习grunt的时候光是配置就把人搞得头大，但是看了gulp的配置文件，我觉得一个小时就能上手。

熟悉Linux的童鞋一定知道一个词：管道！ gulp的自动化构建过程就是利用了管道（pipe）的方式，gulp将获取到的文件内容以stream流的方式给各种插件，以供这些插件来操作；gulp的另一个简单是他只有简单的四个API：`gulp.src`, `gulp.dest`, `gulp.task`, `gulp.watch`   

关于这四个命令的用法在 [gulp API 文档](http://www.gulpjs.com.cn/docs/api/) 有详细介绍，这里不再赘述。

# gulpfile.js 配置  

```js
/**
 * gulp demo 配置文件
 * 根据此学习配置文件，可以在使用时适当修改应用
 * @author ： wangxb
 * @date   ： 2015-12-27
 */

// 引入插件文件，根据需要适时添加修改
var gulp = require('gulp');  
var imagemin = require('gulp-imagemin');       // 实现图片压缩
var uglify = require('gulp-uglify');           // 通过UglifyJS来压缩JS文件
var minify = require('gulp-minify');
var sourcemaps = require('gulp-sourcemaps');   // 处理JS时，生成SourceMap
var coffee = require('gulp-coffee');		   // 编译coffee代码为Js代码
var concat = require('gulp-concat');           // 合并JS
var del = require('del');                      // 删除文件/文件夹

// 定义相关文件路径
var paths = {
	// 源文件地址
	source : 'src/',
	html   : 'src/html/**/*',
	scripts: ['src/coffee/**/*.coffee'],
	images : 'src/images/**/*',
	css    : 'src/sass/**/*',

	// 发布文件地址
	build:    'dist/',
	buildHtml:'dist/html',
	buildJs:  'dist/js/',
	buildCss: 'dist/css/',
	buildImg: 'dist/images/',
};

var minJsName = 'all.min.js';

// 清除旧编译文件
gulp.task('clean', function(){
	del([paths.build]);
});
// 清除文件
gulp.task('clean:html', function(){
	del([paths.buildHtml]);
});
// 清除js文件
gulp.task('clean:script', function(){
	del([paths.buildJs]);
});
// 清除css文件
gulp.task('clean:css', function(){
	del([paths.buildCss]);
});
// 清除图片文件
gulp.task('clean:image', function(){
	del([paths.buildImg]);
});

// 编译、处理、发布html文件
gulp.task('html', ['clean:html'], function(){

});

// 编译、处理、发布脚本文件
gulp.task('script', ['clean:script'], function(){
	return gulp.src(paths.scripts)
			.pipe(sourcemaps.init())     
			.pipe(uglify())              
			.pipe(concat(minJsName))  
			.pipe(sourcemaps.write())
			.pipe(gulp.dest(paths.buildJs));
});

// 编译、处理、发布样式文件
gulp.task('css', ['clean:css'], function(){

});

// 发布静态图片文件
gulp.task('image', ['clean:image'], function(){
	return gulp.src(paths.images)
    		.pipe(imagemin({optimizationLevel: 5}))
    		.pipe(gulp.dest(paths.buildImg));
});

// 监控文件变化，同时在文件变化时执行一些操作
gulp.task('watch', function() {
  gulp.watch(paths.scripts, ['script']);
  gulp.watch(paths.css, ['css']);
  gulp.watch(paths.images, ['image']);
});

/***************************************************
// 开发执行
gulp.task('dev', ['clean'], function(){
})

// 测试执行
gulp.task('qa', function(){

});

// 上线执行
gulp.task('product', function(){

});
****************************************************/
// 默认执行动作
gulp.task('default', ['watch', 'script', 'css', 'image'], function() {     
     // 将你的默认的任务代码放在这  
     console.log('hello gulp');  
});
```

** 其他gulpfile.js ** 示例：

* [gulp官方网站给出的示例](https://github.com/gulpjs/gulp)     

# 总结
虽然这里制作了一个自己觉得还不错的配置，但是毕竟还没有在项目中使用，以后有待验证。还有就是关于前端和前端自动化本人还在努力学习中，对于以上内容会根据理解随时修改加以完善！
