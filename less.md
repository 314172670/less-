# Less #
**定义变量**
##
    @nice-blue: #5B83AD;
    @light-blue: @nice-blue + #111;

**Mixins写法**
##

	.bordered {
	  border-top: dotted 1px black;
	  border-bottom: solid 2px black;
	}
	#menu a {
	  color: #111;
	  .bordered;
	}

若不想混合被输出可以将以上改为：

	.bordered() {
		  border-top: dotted 1px black;
		  border-bottom: solid 2px black;
		}
- 带参数

		//1
		.border-radius(@radius: 5px) {
		  -webkit-border-radius: @radius;
		     -moz-border-radius: @radius;
		          border-radius: @radius;
		}
		#header {
		  .border-radius;
		}

		//2
		.mixin(@color) {
		  color-1: @color;
		}
		.mixin(@color; @padding:2) {
		  color-2: @color;
		  padding-2: @padding;
		}
		.mixin(@color; @padding; @margin: 2) {
		  color-3: @color;
		  padding-3: @padding;
		  margin: @margin @margin @margin @margin;
		}
		.some .selector div {
		  .mixin(#008000);
		}

        //输出
		// .some .selector div {
		//   color-1: #008000;
		//   color-2: #008000;
		//   padding-2: 2;
		// }

        //3
		// .mixin(@color: black; @margin: 10px; @padding: 20px) {
		//   color: @color;
		//   margin: @margin;
		//   padding: @padding;
		// }
		// .class1 {
		//   .mixin(@margin: 20px; @color: #33acfe);
		// }
		// .class2 {
		//   .mixin(#efca44; @padding: 40px);
		// }

- 作为函数使用的Mixin
		.average(@x, @y) {
		  @average: ((@x + @y) / 2);
		}
		
		div112 {
		  .average(16px, 50px); // "call" the mixin
		  padding: @average;    // use its "return" value
		}
		
		//输出
		// div {
		//   padding: 33px;
		// }
- 多值匹配拓展

        //1
		.mixin15 (@a) when (lightness(@a) >= 50%) {
		  background-color: black;
		}
		.mixin15 (@a) when (lightness(@a) < 50%) {
		  background-color: white;
		}
		.mixin15 (@a) {
		  color: @a;
		}
		.class115 { .mixin15(#ddd) }
		.class215 { .mixin15(#555) }

        //2
		 @media: mobile;
		
		.mixin (@a) when (@media = mobile) { ... }
		.mixin (@a) when (@media = desktop) { ... }
		
		.max (@a; @b) when (@a > @b) { width: @a }
		.max (@a; @b) when (@a < @b) { width: @b }

		//3类型检查
		.mixin (@a; @b: 0) when (isnumber(@b)) { ... }
		.mixin (@a; @b: black) when (iscolor(@b)) { ... }
		
		Here are the basic type checking functions:
		iscolor
		isnumber
		isstring
		iskeyword
		isurl
		ispixel
		ispercentage
		isem
		isunit

		//4多条件
		.mixin (@a) when (@a > 0) { ...  }
		.mixin (@a) when (default()) { ... } // matches only if first mixin does not, i.e. when @a <= 0
		
		.mixin (@a) when (isnumber(@a)) and (@a > 0) { ... }
		
		.mixin (@b) when not (@b > 0) { ... }


**嵌套写法**
##
    //less
	.clearfix {
	  display: block;
	  zoom: 1;
	
	  &:after {
	    content: " ";
	    display: block;
	    font-size: 0;
	    height: 0;
	    clear: both;
	    visibility: hidden;
	  }
	}				
	
	//css
	.clearfix {
	  display: block;
	  zoom: 1;
	}
	.clearfix:after {
	  content: " ";
	  display: block;
	  font-size: 0;
	  height: 0;
	  clear: both;
	  visibility: hidden;
	}	
	
**使用函数**	
##
	@base: #f04615;
	@width: 0.5;
	
	.class {
	  width: percentage(@width); // 0.5 转换为 50%
	  color: saturate(@base, 5%);//将颜色饱和度增加 5%
	  background-color: spin(lighten(@base, 25%), 8);  //颜色亮度降低 25% 并且色相值增加 8 
	}			
**命名空间**	
##
	//less
	#bundle {
	  .button {
	    display: block;
	    border: 1px solid black;
	    background-color: grey;
	    &:hover {
	      background-color: white
	    }
	  }
	}
	//我们想使用以上.button的样式，可以用以下这种写法
	#header a {
		  color: orange;
		  #bundle > .button;
		}

    //css
	#bundle .button {
	  display: block;
	  border: 1px solid black;
	  background-color: grey;
	}
	#bundle .button:hover {
	  background-color: white;
	}
	#header a {
	  color: orange;
	  display: block;
	  border: 1px solid black;
	  background-color: grey;
	}
	#header a:hover {
	  background-color: white;
	}
**Scope**
##
	@var: red;
	
	#page {
	  @var: white;
	  #header {
	    color: @var; // white
	  }
	}
    
    //以下情况也相同
	 @var: red;
	
	 #page {
	   #header {
	     color: @var; // white
	   }
	   @var: white;
	 }
**值插入**
##
- Selectors

		// Variables
		@mySelector: banner;
		
		// Usage
		.@{mySelector} {
		  font-weight: bold;
		  line-height: 40px;
		  margin: 0 auto;
		}

- URLs

		// Variables
		@images: "../img";
		
		// 用法
		body {
		  color: #444;
		  background: url("@{images}/white-sand.png");
		}
- import声明

		//Variables
		@themes: "../../src/themes";
		
		//Usage
		 @import "@{themes}/tidal-wave.less";
- 属性

		 @property: color;
		
		.widget {
		   @{property}: #0ee;
		   background-@{property}: #999;
		 }

**extend继承**
##
	// Extend 语法
	 .a:extend(.b) {}
	//the above block does the same thing as the below block
	 .a {
	   &:extend(.b);
	 }
	例子：

    //less
	nav ul {
	  &:extend(.inline);
	  background: blue;
	}
	.inline {
	  color: red;
	}
    
    //css
    nav ul {
	  background: blue;
	}
	.inline,
	nav ul {
	  color: red;
	}
> 注意：这个编译为css和mixins的区别

拓展：

	// .c:extend(.d all) {
	// extends all instances of ".d" e.g. ".x.d" or ".d.x"
	// }
	// .c:extend(.d) {
	// extends only instances where the selector will be output as just ".d"
	// }
	
	// .e:extend(.f) {}
	// .e:extend(.g) {}
	// the above an the below do the same thing
	// .e:extend(.f, .g) {}

	// .bucket {
	//   tr { // nested ruleset with target selector
	//     color: blue;
	//   }
	// }
	// .some-class:extend(.bucket tr) {} // nested ruleset is recognized
	// --Outputs--
	// .bucket tr,
	// .some-class {
	//   color: blue;
	// }

	// Extend "all"
	// .a.b.test,
	// .test.c {
	//   color: orange;
	// }
	// .test {
	//   &:hover {
	//     color: green;
	//   }
	// }
	
	// .replacement:extend(.test all) {}
	//--Outputs--
	// .a.b.test,
	// .test.c,
	// .a.b.replacement,
	// .replacement.c {
	//   color: orange;
	// }
	// .test:hover,
	// .replacement:hover {
	//   color: green;
	// }

	//继承在一个media声明内部不能匹配内部的选择器
	// @media screen {
	//   .screenClass:extend(.selector) {} // extend inside media
	//   @media (min-width: 1023px) {
	//     .selector {  // ruleset inside nested media - extend ignores it
	//       color: blue;
	//     }
	//   }
	// }
	//--Output--
	// @media screen and (min-width: 1023px) {
	//   .selector { /* ruleset inside another nested media was ignored */
	//     color: blue;
	//   }
	// }

	//此情况可以
	// @media screen {
	//   .selector {  /* ruleset inside nested media - top level extend works */
	//     color: blue;
	//   }
	//   @media (min-width: 1023px) {
	//     .selector {  /* ruleset inside nested media - top level extend works */
	//       color: blue;
	//     }
	//   }
	// }
	
	// .topLevel:extend(.selector) {} /* top level extend matches everything */
	//--Output--
	// @media screen {
	//   .selector,
	//   .topLevel { /* ruleset inside media was extended */
	//     color: blue;
	//   }
	// }
	// @media screen and (min-width: 1023px) {
	//   .selector,
	//   .topLevel { /* ruleset inside nested media was extended */
	//     color: blue;
	//   }
	// }

	//必须写在选择器后面
	//pre:hover:extend(div pre)  或者 pre:hover :extend(div pre)
	//pre:hover:extend(div pre):extend(.bucket tr)  ==等于==   pre:hover:extend(div pre, .bucket tr)
	//不允许这样  pre:hover:extend(div pre).nth-child(odd)
	//继承不允许匹配带值的选择器，如下1-1和1-2都是不允许的：
	//1-1
	// @variable: .bucket;
	// @{variable} { // interpolated selector
	//   color: blue;
	// }
	// .some-class:extend(.bucket) {} // does nothing, no match is found
	//1-2
	// .bucket {
	//   color: blue;
	// }
	// .some-class:extend(@{variable}) {} // interpolated selector matches nothing
	// @variable: .bucket;
	//以上两个例子输出：
	// .bucket {
	//   color: blue;
	// }
	//1-3这种情况是允许的
	// .bucket {
	//   color: blue;
	// }
	// @{variable}:extend(.bucket) {}
	// @variable: .selector;

**important关键字会被继承**
##
	.foo (@bg: #f5f5f5, @color: #900) {
	  background: @bg;
	  color: @color;
	}
	.unimportant {
	  .foo(1);
	}
	.important {
	  .foo(2) !important;
	}
	
	//输出以下
	// .unimportant {
	//   background: #f5f5f5;
	//   color: #900;
	// }
	// .important {
	//   background: #f5f5f5 !important;
	//   color: #900 !important;
	// }
**@arguments使用，以及@rest**

- @arguments
	
	.box-shadow(@x: 0; @y: 0; @blur: 1px; @color: #000) {
	  -webkit-box-shadow: @arguments;
	     -moz-box-shadow: @arguments;
	          box-shadow: @arguments;
	}
	.big-block {
	  .box-shadow(2px; 5px);
	}
	
	//输出
	// .big-block {
	//   -webkit-box-shadow: 2px 5px 1px #000;
	//      -moz-box-shadow: 2px 5px 1px #000;
	//           box-shadow: 2px 5px 1px #000;
	// }


- @rest

		Advanced arguments and the @rest variable
		 .mixin(...) {        // matches 0-N arguments
		 .mixin() {           // matches exactly 0 arguments
		 .mixin(@a: 1) {      // matches 0-1 arguments
		 .mixin(@a: 1; ...) { // matches 0-N arguments
		 .mixin(@a; ...) {    // matches 1-N arguments
		
		 .mixin(@a; @rest...) {
		    // @rest is bound to arguments after @a
		   // @arguments is bound to all arguments
		 }

**多值匹配**
##
	.mixin14(dark; @color) {
	  color: darken(@color, 10%);
	}
	.mixin14(light; @color) {
	  color: lighten(@color, 10%);
	}
	.mixin14(@_; @color) {
	  display: block;
	}
	
	@switch: light;
	
	.class14 {
	  .mixin14(@switch; #888);
	}

**CSS**
##
	& when (@my-option = true) {
	  button {
	    color: white;
	  }
	  a {
	    color: blue;
	  }
	}

**递归循环**
##
	.generate-columns(4);
	
	.generate-columns(@n, @i: 1) when (@i =< @n) {
	  .column-@{i} {
	    width: (@i * 100% / @n);
	  }
	  .generate-columns(@n, (@i + 1));
	}
	
	//输出
	// .column-1 {
	//   width: 25%;
	// }
	// .column-2 {
	//   width: 50%;
	// }
	// .column-3 {
	//   width: 75%;
	// }
	// .column-4 {
	//   width: 100%;
	// }

**合并属性**
##
	//为了避免意外的合并，merge 需要在每个需要合并的属性名后面添加一个 + 以作标示。
	.mixin18() {
	  box-shadow+: inset 0 0 10px #555;
	}
	.myclass18 {
	  .mixin18();
	  box-shadow+: 0 0 20px black;
	}
	//输出为
	// .myclass18 {
	//   box-shadow: inset 0 0 10px #555, 0 0 20px black;
	// }

**父选择符**
##
    //1
	a {
	  color: blue;
	  &:hover {
	    color: green;
	  }
	}
	//输出为
	a {
	  color: blue;
	}
	
	a:hover {
	  color: green;
	}

	//2
	.button {
	  &-ok {
	    background-image: url("ok.png");
	  }
	  &-cancel {
	    background-image: url("cancel.png");
	  }
	
	  &-custom {
	    background-image: url("custom.png");
	  }
	}

	//3 多个&
	.link {
	  & + & {
	    color: red;
	  }
	
	  & & {
	    color: green;
	  }
	
	  && {
	    color: blue;
	  }
	
	  &, &ish {
	    color: cyan;
	  }
	}
	//输出为
	// .link + .link {
	//   color: red;
	// }
	// .link .link {
	//   color: green;
	// }
	// .link.link {
	//   color: blue;
	// }
	// .link, .linkish {
	//   color: cyan;
	// }

	//4 &指的是所有父节点，不仅仅只是最近的祖先
	.grand {
	  .parent {
	    & > & {
	      color: red;
	    }
	
	    & & {
	      color: green;
	    }
	
	    && {
	      color: blue;
	    }
	
	    &, &ish {
	      color: cyan;
	    }
	  }
	}
	
	//输出为
	// .grand .parent > .grand .parent {
	//   color: red;
	// }
	// .grand .parent .grand .parent {
	//   color: green;
	// }
	// .grand .parent.grand .parent {
	//   color: blue;
	// }
	// .grand .parent,
	// .grand .parentish {
	//   color: cyan;
	// }

    //5
	p, a, ul, li {
	border-top: 2px dotted #366;
	  & + & {
	      border-top: 0;
	  }
	}
	
	//输出为
	p,
	a,
	ul,
	li {
	  border-top: 2px dotted #366;
	}
	p + p,
	p + a,
	p + ul,
	p + li,
	a + p,
	a + a,
	a + ul,
	a + li,
	ul + p,
	ul + a,
	ul + ul,
	ul + li,
	li + p,
	li + a,
	li + ul,
	li + li {
	  border-top: 0;
	}
**导出选项**
##
使用如下：
@import (reference) "foo.less";  //其中括号内为可选项
> reference: 
> 
> use a Less file but do not output it
> This allows you to pull in only specific, targeted styles from a library such as Bootstrap by doing something like this:
> .navbar:extend(.navbar all) {}
> And you will pull in only .navbar related styles from Bootstrap
> 
> inline: include the source file in the output but do not process it
> 
> less: treat the file as a Less file, no matter what the file extension
> 
> css: treat the file as a CSS file, no matter what the file extension
> 
> once: only include the file once (this is default behavior)//文件只导入一次，后面导入的将被忽略
> 
> multiple: include the file multiple times  //文件可导入多次，与上面相反

	Example:
	// file: foo.less
	.a {
	  color: green;
	}
	// file: main.less
	@import (multiple) "foo.less";
	@import (multiple) "foo.less";
	
	Outputs：
	.a {
	  color: green;
	}
	.a {
	  color: green;
	}