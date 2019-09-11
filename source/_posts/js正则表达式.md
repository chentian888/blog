---
title: js正则表达式
toc: true
date: 2019-09-11 11:53:34
categories: 笔记
tags: js
---

# js正则表达式
## RegExp下的方法
`test`在字符串中测试模式匹配，成功返回`true`，失败返回`false`
`exec`在字符串中执行匹配搜索，返回结果数组

## 字符串的正则表达式方法

| 方法                         | 含义                              |
| -                            |                                   |
| match(pattern)               | 返回pattern中的子串或null         |
| replace(pattern,replacement) | 用replacement替换pattern          |
| search(pattern)              | 返回字符串中的pattern开始位置     |
| split(pattern)               | 返回字符串中按指定pattern拆分数组 |

## 静态属性

| 属性         | 短名 | 含义                                   |
| -            |      |                                        |
| input        | $_   | 当前被匹配的字符串                     |
| lastMatch    | $&   | 最后一个匹配字符串                     |
| lastParen    | $+   | 最后一对圆括号内的匹配字符串           |
| leftContext  | $`   | 最后一次匹配前的字符串                 |
| rightContext | $*   | 最后一次匹配后的字符串                 |
| multiline    | $'   | 指定是否所有的表达式都用于多行的布尔值 |

## 实例属性

| 属性       | 含义                                   |
| -          |                                        |
| global     | Boolean值，表示g是否已设置             |
| ignoreCase | Boolean值，表示i是否已设置             |
| lastIndex  | 整数，代表下次匹配将从哪个字符位置开始 |
| multiline  | Boolean值，表示m是否已经设置           |
| Source     | 正则表达式的源字符串形式               |

## 获取控制
正则表达式元字符是包含特殊含义的字符，他们有一些特殊的功能，可以控制匹配模式的方式。反斜杠后的元字符将失去其特殊含义。

**字符类：单个字符和数字**

| 元字符/元符号 | 匹配情况                           |
| -             |                                    |
| .             | 匹配除换行符外的任意字符           |
| [a-z0-9]      | 匹配括号中的字符集中的任意字符     |
| [^a-z0-9]     | 匹配任意不在括号中的字符集中的字符 |
| \d            | 匹配数字                           |
| \D            | 匹配非数字，同[^0-9]相同           |
| \w            | 匹配字母和数字及_                  |
| \W            | 匹配非字母和数字及_                |

**字符类：空白字符**

| 元字符/元符号 | 匹配情况                           |
| -             |                                    |
| \0            | 匹配null 字符                      |
| \b            | 匹配空格字符                       |
| \f            | 匹配进纸字符                       |
| \n            | 匹配换行符                         |
| \r            | 匹配回车字符                       |
| \t            | 匹配制表符                         |
| \s            | 匹配空白字符、空格、制表符和换行符 |
| \S            | 匹配非空白字符                     |

**字符类：锚字符**

| 元字符/元符号 | 匹配情况                     |
| -             |                              |
| ^             | 行首匹配                     |
| &             | 行尾匹配                     |
| \A            | 只匹配字符串开始处           |
| \b            | 匹配单词边界，词在[]内时无效 |
| \B            | 匹配非单词边界               |
| \G            | 匹配当前搜索的开始位置       |
| \Z            | 匹配字符串结束处或行尾       |
| \z            | 只匹配字符串结束处           |

**字符类：重复字符**

| 元字符/元符号 | 匹配情况                |
| -             |                         |
| x?            | 匹配0 个或1 个x         |
| x*            | 匹配0 个或任意多个x     |
| x+            | 匹配至少一个x           |
| (xyz+)        | 匹配至少一个(xyz)       |
| x{m,n}        | 匹配最少m 个、最多n 个x |

**字符类：替代字符**

| 元字符/元符号   | 匹配情况                           |
| this|where|logo | 匹配this 或where 或logo 中任意一个 |

**字符类：记录字符**

| 元字符/元符号 | 匹配情况               |
| -             |                        |
| (string)      | 用于反向引用的分组     |
| \1 或$1       | 匹配第一个分组中的内容 |
| \2 或$2       | 匹配第二个分组中的内容 |
| \3 或$3       | 匹配第三个分组中的内容 |

注意：如果想使用记录字符，必须要在使用前运行一下

```js
var pattern=/8(.*)8/;
var str='this is a 8google8';
pattern.test(str);
//如果想使用记录字符，必须要运行一下，用test，exec，match，search，split都行
console.log(str.replace(pattern,'<strong>$1</strong>'));
```

## 贪婪-惰性
在贪婪后面加上`?`就是惰性

| 贪婪  | 惰性   |
| -     |        |
| +     | +?     |
| ?     | ??     |
| *     | *?     |
| {n}   | {n}?   |
| {n,}  | {n,}?  |
| {n,m} | {n,m}? |

贪婪
```js
    var pattern=/[a-z]+/;
    var str='asdfasdfaqwuiouopjlkj';
    var result=str.replace(pattern,'1');
    console.log(result);//贪婪，所有的字符串都变为了1
```

惰性

```js
    var pattern=/[a-z]+?/;
    var str='asdfasdfaqwuiouopjlkj';
    var result=str.replace(pattern,'1');
    console.log(result);//惰性，只有第一个字母被替换成1
    //如果在惰性模式上加上全局匹配
    var pattern=/[a-z]+?/g;//这种情况相当于贪婪模式，所有的字符都会别替换为1
```

经典列子：

```js
    var pattern=/8(.*)8/;
    var str='8google8 8google8 8google8 8google8';
    document.write(str.replace(pattern,'<strong>$1</strong>'));
    //输出结果<strong>google8 8google8 8google8 8google</strong>
```

贪婪模式导致正则表达式匹配到`google8 8google8 8google8 8google`，而不是匹配`8google8`。
改为惰性模式

```js
    var pattern=/8(.*?)8/;
    var str='8google8 8google8 8google8 8google8';
    document.write(str.replace(pattern,'<strong>$1</strong>'));
    //输出结果,只有第一个google被加粗
```

并开启全局模式

```js
    var pattern=/8(.*?)8/g;
    var str='8google8 8google8 8google8 8google8';
    document.write(str.replace(pattern,'<strong>$1</strong>'));
    //输出结果,所有google被加粗
```

## RegExp.exec(str)
说明：返回匹配到的数组.使用正则表达式模式对字符串执行搜索，并将更新全局RegExp对象的属性以反映匹配结果。如果没有匹配的文本则返回null，否则返回一个结果数组。
返回结果类似下列各式：

    ["google 2017", "google", "2017", index: 0, input: "google 2017"]

* `index`声明匹配文本的第一个字符的位置
* `input`存放被检索的字符串string

```js
    var pattern = /^[a-z]+\s[0-9]{4}$/i;
    var str = 'google 2012';
    console.log(pattern.exec(str));//返回整个字符串

    var pattern = /^[a-z]+/i; 
    var str = 'google 2012';
    console.log(pattern.exec(str));//只匹配字母google
```

使用分组匹配

```js
    var pattern = /^([a-z]+)\s([0-9]+)/i;//使用分组
    var str = 'google 2012';
    var result=pattern.exec(str);
    console.log(result);//此时的result是个数组
    console.log(result[0]);//第1位总是存放当前用于匹配的字符串 google 2012
    console.log(result[1]);//第2位存放第1个分组匹配到的内容
    console.log(result[2]);//第3位存放第2个分组匹配到的内容，以此类推
```

## 捕获性分组 
说明：所有的分组都捕获返回

```js
    var pattern = /([a-z]+)([0-9]+)/i;
    var str = 'google2012';
    var result=pattern.exec(str);
    console.log(result[0]);//google2012
    console.log(result[1]);//google
    console.log(result[2]);//2012
```

## 非捕获性分组
说明：非捕获性分组只需要在不需要捕获的分组前加上`?:`

```js
    var pattern = /([a-z]+)(?:[0-9]+)/i;
    var str = 'google2012';
    var result=pattern.exec(str);
    console.log(result[0]);//google2012
    console.log(result[1]);//google
    console.log(result[2]);//undefined
```

## 分组嵌套
说明：嵌套分组从外往内获取

```js
    var pattern = /(A?(B?(C?)))/;
    var str = 'ABC';
    var result=pattern.exec(str);
    console.log(result[0]);//ABC
    console.log(result[1]);//ABC
    console.log(result[2]);//BC
```

## 正向前瞻
说明：匹配后面满足表达式exp的位置。字符串后面必须跟着某一个特定的字符才能被捕获，例如：`goo`后面必须跟着`gle`才能捕获 `goo(?=gle)`

```js
    var pattern = /goo(?=gle)/;
    var str = 'google';
    var result=pattern.exec(str);

    var str = 'Hello, Hi, I am Hilary.';
    var reg = /H(?=i)/g;
    var newStr = str.replace(reg, "T");
    console.log(newStr);//Hello, Ti, I am Tilary.
```

在这个DEMO中我们可以看出正向前瞻的作用，同样是字符"H"，但是只匹配"H"后面紧跟"i"的"H"

## 负向前瞻
说明：匹配后面不满足表达式exp的位置。
注意：正则表达式从文本头部向尾部开始解析，文本尾部方向成为`前`，文本头部方向成为`尾`。
正向前瞻就是在正则表达式匹配到规则的时候，向前(文本尾部)检查是否符合断言。
负向前瞻就是在正则表达式匹配到规则的时候，向前(文本尾部)检查是否不符合断言，只有不符合的情况才能匹配。
js不支持后瞻。
例如：例如：`goo`后面必须不能跟着`gle`才能捕获 `goo(?!gle)`

```js
    var str = 'Hello, Hi, I am Hilary.';
    var reg = /H(?!i)/g;
    var newStr = str.replace(reg, "T");
    console.log(newStr);//Tello, Hi, I am Hilary.
```

在这个DEMO中，我们把之前的正向前瞻换成了负向前瞻。这个正则的意思就是，匹配"H",且后面不能跟着一个"i"。这时候"Hello"就可以成功匹配

**注意：**
1. 前瞻是非捕获性分组
2. 前瞻不消耗字符，前瞻只匹配满足前瞻表达式的字符，而不匹配其本身。

```js
    //前瞻是非捕获性分组
    var str = 'Hello, Hi, I am Hilary.';
    var reg = /H(?=i)/g;
    var newStr = str.replace(reg, "$1");
```

`$1`匹配不到任何分组信息

## 后瞻
js不支持后瞻，这里暂时不做讨论

```js
    (?<=exp) 正向后瞻 匹配前面满足表达式exp的位置（JS不支持）
    (?<!exp) 负向后瞻 匹配前面不满足表达式exp的位置（JS不支持）
```

## 特殊字符匹配
说明：用`\`来转义正则里面的特殊字符

```js
    var pattern = /\.\[\/b\]/; //特殊字符，用\符号转义即可
    var str = '.[/b]';
    console.log(pattern.test(str));
```

## 换行模式
说明：

```js
    var pattern = /^\d+/mg; //启用了换行模式
    var str = '1.baidu\n2.google\n3.bing';
    var result = str.replace(pattern, '#');
    console.log(result);
```

