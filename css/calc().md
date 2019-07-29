# calc() 
此CSS函数让你在声明CSS属性值时执行一些计算。

## 定义与用法
calc() 函数用于动态计算长度值。

- 需要注意的是，运算符前后都需要保留一个空格，例如：width: calc(100% - 10px)；
- 任何长度值都可以使用calc()函数进行计算；
- calc()函数支持 
    - "+", 
    - "-", 
    - "*"(乘数中至少要有一个是 <number> 类型的), 
    - "/"(除数（/右面的数）必须是 <number> 类型的) 运算；
- calc()函数使用标准的数学运算优先级规则；

> 用 0 作除数会让 HTML 解析器抛出异常.

> `+` 和 `-` 运算符的两边必须始终要有空白符。比如 `calc(50% -8px)` 会被解析成为一个无效的表达式：一个百分比后跟一个负数长度值。而 `calc(8px + -50%)` 会被解析成为一个长度后跟一个加号再跟一个负百分比。

> `*` 和 `/` 运算符不需要空白符，但考虑到统一性，仍然推荐加上空白符。

> 注： 涉及自动和固定布局表中的表列，表列组，表行，表行组和表单元格的宽度和高度百分比的数学表达式可视为已指定auto。

## 示例

```css
input {
  padding: 2px;
  display: block;
  width:  98%; 
/* fallback for browsers without support for calc() */
  width:  calc(100% - 1em);  
}

#formbox {
  width:  130px; 
/* fallback for browsers without support for calc() */
  width:  calc(100% / 6);   
  border: 1px solid black;
  padding: 4px;
}
```