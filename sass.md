# sass

sass 是一种css扩展语法，本文记录sass的一些常用知识点，详细知识点参考[sass官方文档](https://sass-lang.com/documentation)

## 环境准备

在vscode中安装`Live Sass Compiler`插件，然后编写`.sass`或`.scss`文件（scss文件和sass文件的区别在与编码风格不同，前者类似Python使用缩进，后者类似原生css使用{}）按住Ctrl/Cmd + Shift + P输入Watch sass就可以让编译器能够将上述文件类型转换成`.css`文件

## 语法嵌套

以前写css语法的时候，有些代码会重复出现

```css
div span{
  font-size:20px;
}
div span a{
  color:red;
}
```

但使用scss的嵌套语法就可以避免，而且结构层次也很清晰

```css
div {
	span{
		font-size:20px;
		a{
			color:red;
		}
	}
}
```



## 语法简写

原css写法

```css
div span{
  color:red;
}
div span:active{
  color:blue;
}
div span:hover{
  color:green;
}
```

scss写法

```css
div{
  span{
    color:red;
  }
  &:active{
    color:blue;
  }
  &:hover:green;
}
```

## 变量Variables

对于一些常用的值，可以定义一个变量

原css写法

```css
div{
    font-size:100px;
    color: #333;
}
span{
    font-size:100px;
    color:#333;
}
h1{
    font-size:100px;
    color:#333;
}
```

scss可以使用`$`添加变量

```css
$font-size : 100px;
$color: #333;

div {
    font-size: $font-size;
    color: $color;
}

span {
    font-size: $font-size;
    color: $color;
}

h1 {
    font-size: $font-size;
    color: $color;
}
```

## 引入其他scss文件

通过`@import`可以引入其他scss

在index.sass中引入other.scss

other.scss

```css
$font-size:200px;
div {
    font-size: $font-size;
    color: $color;
}
```

index.scss

```css
$font-size : 100px;
$color: #333;
@import 'other';

span {
    font-size: $font-size;
    color: $color;
}

h1 {
    font-size: $font-size;
    color: $color;
}
```

可以看看最后生成的css结果长这样：

```css
div {
  font-size: 100px;
  color: #333;
}

span {
  font-size: 100px;
  color: #333;
}

h1 {
  font-size: 100px;
  color: #333;
}
/*# sourceMappingURL=index.css.map */
```

好像和文件拆分前没什么不同，不过这也有个小细节，就是变量$font-size最终使用了index.scss里的100px

还有一点需要注意，每个`.scss`文件都会生成一个对应的`.css`文件，但是有时候可能有的`.scss`文件只是存放一些变量，比如variable.sass:

```css
$font-size : 100px;
$color: #333;
```

那么它没有必要生成对应的`.css`文件，这时候只需要在文件名加个`_前缀`就可以了，不过引用的时候依然可以直接使用variable这个名字

index.scss

```css
@import 'other';
@import 'variable'
span {
    font-size: $font-size;
    color: $color;
}

h1 {
    font-size: $font-size;
    color: $color;
}
```

## Mixin

像刚才通过@import的方式可以很方便的引用某个样式，但是如果引用的不是某个样式，而是某些常用的属性怎么办呢，这个时候mixin就登场了

原css中overflow:hidden;white-space:nowrap;text-overflow:ellipsis;这三条属性经常被使用

```css
.text{
  width:600px;
  overflow:hidden;
  white-space:nowrap;
  text-overflow:ellipsis;
}

.content-text{
  width:1000px;
  overflow:hidden;
  white-space:nowrap;
  text-overflow:ellipsis;
}
```

scss使用@mixin提取，使用@include引用

```css
@mixin singleline-ellipsis {
  overflow:hidden;
  white-space:nowrap;
  text-overflow:ellipsis;
}

.text{
  width:600px;
  @include singleline-ellipsis;
}

.content-text{
  width:1000px;
  @include singleline-ellipsis;
}
```





