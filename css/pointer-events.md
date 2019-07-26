# pointer-events

pointer-events CSS 属性指定在什么情况下 (如果有) 某个特定的图形元素可以成为鼠标事件的 target。

> 场景: 当父节点button绑定点击事件, 鼠标点击在子元素svg上可能触发不了事件
那么我们就可以使用 `pointer-events: none`让子元素不成为鼠标事件的target。这样就能避免事件不触发的情况了.

## 值

```css
/* Keyword values */
pointer-events: auto;
pointer-events: none;
pointer-events: visiblePainted; /* SVG only */
pointer-events: visibleFill;    /* SVG only */
pointer-events: visibleStroke;  /* SVG only */
pointer-events: visible;        /* SVG only */
pointer-events: painted;        /* SVG only */
pointer-events: fill;           /* SVG only */
pointer-events: stroke;         /* SVG only */
pointer-events: all;            /* SVG only */

/* Global values */
pointer-events: inherit;
pointer-events: initial;
pointer-events: unset;
```

## 属性(MDN)

### auto
与pointer-events属性未指定时的表现效果相同，对于SVG内容，该值与visiblePainted效果相同

### none
元素永远不会成为鼠标事件的target。但是，当其后代元素的pointer-events属性指定其他值时，鼠标事件可以指向后代元素，在这种情况下，鼠标事件将在捕获或冒泡阶段触发父元素的事件侦听器。

### visiblePainted
只适用于SVG。元素只有在以下情况才会成为鼠标事件的目标：
 
visibility属性值为visible，且鼠标指针在元素内部，且fill属性指定了none之外的值
visibility属性值为visible，鼠标指针在元素边界上，且stroke属性指定了none之外的值

### visibleFill
只适用于SVG。只有在元素visibility属性值为visible，且鼠标指针在元素内部时,元素才会成为鼠标事件的目标，fill属性的值不影响事件处理。

### visibleStroke
只适用于SVG。只有在元素visibility属性值为visible，且鼠标指针在元素边界时,元素才会成为鼠标事件的目标，stroke属性的值不影响事件处理。

### visible
只适用于SVG。只有在元素visibility属性值为visible，且鼠标指针在元素内部或边界时,元素才会成为鼠标事件的目标，fill和stroke属性的值不影响事件处理。

### painted
只适用于SVG。元素只有在以下情况才会成为鼠标事件的目标：
 
鼠标指针在元素内部，且fill属性指定了none之外的值
鼠标指针在元素边界上，且stroke属性指定了none之外的值
visibility属性的值不影响事件处理。

### fill
只适用于SVG。只有鼠标指针在元素内部时,元素才会成为鼠标事件的目标，fill和visibility属性的值不影响事件处理。

### stroke
只适用于SVG。只有鼠标指针在元素边界上时,元素才会成为鼠标事件的目标，stroke和visibility属性的值不影响事件处理。

### all
只适用于SVG。只有鼠标指针在元素内部或边界时,元素才会成为鼠标事件的目标，fill、stroke和visibility属性的值不影响事件处理。