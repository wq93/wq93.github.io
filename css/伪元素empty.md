## 当元素为空时创建一个伪类, 有内容时伪类无法被选中
### 场景
- 加载loding
- 无内容提示

### 示例代码: 
#### css
```css
ul {
  &:empty:after {
    content: "AFTER EMPTY";
    display:block;
    width: 200px;
    height: 200px;
    background: pink;
    line-height: 200px;
    text-align: center;
    
  }  
}
```

#### html
```html
<ul>1</ul>
<ul>
    <li>Item One</li>
    <li>Item Two</li>
</ul>
```
