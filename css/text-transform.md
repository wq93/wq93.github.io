# text-transform
> **text-transform** CSS属性指定如何将元素的文本大写。它可以用于使文本显示为全大写或全小写，也可单独对每一个单词进行操作。
## 适应场景
- 项目中有些按钮上的文字需要全部大写, 
- 有些状态显示需要大写
- ......

### capitalize
这个关键字强制每个单词的首字母转换为大写。其他的字符保留不变（它们写在元素里的文本保留原始大小写）。字母是Unicode字符集或者数字里定义的字符 ；因此单词开头的任何标点符号或者特殊符号将会被忽略。

### uppercase
这个关键字强制所有字符被转换为大写。

### lowercase
这个关键字强制所有字符被转换为小写。

### none
这个关键字阻止所有字符的大小写被转换。

### full-width 
这个关键字强制字符 — 主要是表意字符和拉丁文字 — 书写进一个方形里，并允许它们按照一般的东亚文字（比如中文或日文）对齐。