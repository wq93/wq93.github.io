## ① reduce()方法

### 1、语法

```js
arr.reduce(callback,[initialValue])
```

`reduce` 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，接受四个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 `reduce` 的数组。

```js
callback （执行数组中每个值的函数，包含四个参数）

    1、previousValue （上一次调用回调返回的值(没有提供initialValue)，或者是提供的初始值（initialValue））
    2、currentValue （数组中当前被处理的元素）
    3、index （当前元素在数组中的索引）
    4、array （调用 reduce 的数组）

initialValue （作为第一次调用 callback 的第一个参数。）
```

### 2、实例解析 initialValue 参数

1. 没有提供initialValue参数

   ```js
   var arr = [1, 2, 3, 4];
   var sum = arr.reduce(function(prev, cur, index, arr) {
       console.log(prev, cur, index);
       return prev + cur;
   })
   console.log(arr, sum);
   // 打印结果： 
   // 1 2 1
   // 3 3 2
   // 6 4 3 
   // [1, 2, 3, 4, 5] 15
   ```

   这里可以看出，上面的例子index是从1开始的，第一次的prev的值是数组的第一个值。数组长度是4，但是reduce函数循环3次。

2. 提供initialValue参数

   ```js
   var  arr = [1, 2, 3, 4];
   var sum = arr.reduce(function(prev, cur, index, arr) {
       console.log(prev, cur, index);
       return prev + cur;
   }，0) //注意这里设置了初始值
   console.log(arr, sum);

	// 打印结果： 
	// 0 1 0
	// 1 2 1
	// 3 3 2 
	// 6 4 3
	// [1, 2, 3, 4] 10
   ```

   这个例子index是从0开始的，第一次的prev的值是我们设置的初始值0，数组长度是4，reduce函数循环4次。

结论：`如果没有提供initialValue，reduce 会从索引1的地方开始执行 callback 方法，跳过第一个索引。如果提供initialValue，从索引0开始。`



注意：如果这个数组为空，运用`reduce`是什么情况？

```js
var  arr = [];
var sum = arr.reduce(function(prev, cur, index, arr) {
    console.log(prev, cur, index);
    return prev + cur;
})
//报错，"TypeError: Reduce of empty array with no initial value"
```

但是要是我们设置了初始值就不会报错，如下：

```js
var  arr = [];
var sum = arr.reduce(function(prev, cur, index, arr) {
    console.log(prev, cur, index);
    return prev + cur;
}，0)
console.log(arr, sum); // [] 0
```

**所以一般来说我们提供初始值通常更安全**

### 3、reduce的高级用法

1. 计算数组中每个元素出现的次数

   ```js
   let names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];
   
   let nameNum = names.reduce((pre,cur)=>{
     if(cur in pre){
       pre[cur]++
     }else{
       pre[cur] = 1 
     }
     return pre
   },{})
   console.log(nameNum); //{Alice: 2, Bob: 1, Tiff: 1, Bruce: 1}
   ```

2. 数组去重

   ```js
   let arr = [1,2,3,4,4,1]
   let newArr = arr.reduce((pre,cur)=>{
       if(!pre.includes(cur)){
         return pre.concat(cur)
       }else{
         return pre
       }
   },[])
   console.log(newArr);// [1, 2, 3, 4]
   //-----------------------------------------------
   let arr = [1,2,1,2,3,5,4,5,3,4,4,4,4];
   let result = arr.sort().reduce((init, current)=>{
       if(init.length===0 || init[init.length-1]!==current){
           init.push(current);
       }
       return init;
   }, []);
   console.log(result); //[1,2,3,4,5]
   
   // 不能使用	pre.push(cur) 因为数组的push方法返回的是新数组的长度,而concat返回的是新数组
   ```

3. 将二维数组转化为一维

   ```js
   let arr = [[0, 1], [2, 3], [4, 5]]
   let newArr = arr.reduce((pre,cur)=>{
       return pre.concat(cur)
   },[])
   console.log(newArr); // [0, 1, 2, 3, 4, 5]
   ```

4. 将多维数组转化为一维

   ```js
   let arr = [[0, 1], [2, 3], [4,[5,6,7]]]
   const newArr = function(arr){
      return arr.reduce((pre,cur)=>pre.concat(Array.isArray(cur)?newArr(cur):cur),[])
   }
   console.log(newArr(arr)); //[0, 1, 2, 3, 4, 5, 6, 7]
   ```

5. 对象里的属性求和

   ```js
   var result = [
       {
           subject: 'math',
           score: 10
       },
       {
           subject: 'chinese',
           score: 20
       },
       {
           subject: 'english',
           score: 30
       }
   ];
   
   var sum = result.reduce(function (prev, cur) {
       return cur.score + prev;
   }, 0);
   console.log(sum) //60
   ```

6. 按属性对object分类

   ```js
   var people = [
       { name: 'Alice', age: 21 },
       { name: 'Max', age: 20 },
       { name: 'Jane', age: 20 }
   ];
   
   function groupBy(objectArray, property) {
       return objectArray.reduce(function (acc, obj) {
           console.log(obj)
           var key = obj[property];
           if (!acc[key]) {
               acc[key] = [];
           }
           acc[key].push(obj);
           return acc;
       }, {});
   }
   
   var groupedPeople = groupBy(people, 'age');
   
   // { 
   //   20: [
   //     { name: 'Max', age: 20 }, 
   //     { name: 'Jane', age: 20 }
   //   ], 
   //   21: [{ name: 'Alice', age: 21 }] 
   // }
   ```

7. 功能型函数管道

   ```js
   // Building-blocks to use for composition
   const double = x => x + x;
   const triple = x => 3 * x;
   const quadruple = x => 4 * x;
   
   // Function composition enabling pipe functionality
   const pipe = (...functions) => input => functions.reduce(
       (acc, fn) => fn(acc),
       input
   );
   
   // 还原函数
   // function pipe(...functions) {
   //     return function (input) {
   //         return functions.reduce(function (acc, fn) {
   //             return fn(acc)
   //         }, input)
   //     }
   // }
   
   // Composed functions for multiplication of specific values
   const multiply6 = pipe(double, triple);
   const multiply9 = pipe(triple, triple);
   const multiply16 = pipe(quadruple, quadruple);
   const multiply24 = pipe(double, triple, quadruple);
   
   // Usage
   multiply6(6); // 36
   multiply9(9); // 81
   multiply16(16); // 256
   multiply24(10); // 240
   ```

---



## ② filter()方法

`filter` 为数组中的每个元素调用一次 `callback` 函数，并利用所有使得 `callback` 返回 true 或 [等价于 true 的值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) 的元素创建一个新数组。`callback` 只会在已经赋值的索引上被调用，对于那些已经被删除或者从未被赋值的索引不会被调用。那些没有通过 `callback` 测试的元素会被跳过，不会被包含在新数组中。

### 1、参数

**callback**

	用来测试数组的每个元素的函数。调用时使用参数 (element, index, array)。
返回true表示保留该元素（通过测试），false则不保留。它接受三个参数：

	**element**

		当前在数组中处理的元素。

	**index**可选

		正在处理元素在数组中的索引。

	**array**可选

		调用了`filter`的数组。

**`thisArg`可选**

	可选。执行 `callback` 时的用于 `this` 的值。

### 2、返回值

一个新的通过测试的元素的集合的数组，如果没有通过测试则返回空数组

### 3、返回值

`filter` 为数组中的每个元素调用一次 `callback` 函数，并利用所有使得 `callback` 返回 true 或 [等价于 true 的值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) 的元素创建一个新数组。`callback` 只会在已经赋值的索引上被调用，对于那些已经被删除或者从未被赋值的索引不会被调用。那些没有通过 `callback` 测试的元素会被跳过，不会被包含在新数组中。

`callback` 被调用时传入三个参数：

1. 元素的值
2. 元素的索引
3. 被遍历的数组

如果为 `filter` 提供一个 `thisArg` 参数，则它会被作为 `callback` 被调用时的 `this` 值。否则，`callback` 的 `this` 值在非严格模式下将是全局对象，严格模式下为 `undefined`。
`callback` 最终观察到的`this`值是根据[通常函数所看到的 "this"的规则](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)确定的。

`filter` 不会改变原数组，它返回过滤后的新数组。

`filter` 遍历的元素范围在第一次调用 `callback` 之前就已经确定了。在调用 `filter` 之后被添加到数组中的元素不会被 `filter` 遍历到。如果已经存在的元素被改变了，则他们传入 `callback` 的值是 `filter` 遍历到它们那一刻的值。被删除或从来未被赋值的元素不会被遍历到。

### 4、示例

- 筛选排除所有的小值

```js
function isBigEnough(element) {
  return element >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// filtered is [12, 130, 44]
```

- 过滤JSON中的无效条目

```js
var arr = [
  { id: 15 },
  { id: -1 },
  { id: 0 },
  { id: 3 },
  { id: 12.2 },
  { },
  { id: null },
  { id: NaN },
  { id: 'undefined' }
];

var invalidEntries = 0;

function isNumber(obj) {
  return obj !== undefined && typeof(obj) === 'number' && !isNaN(obj);
}

function filterByID(item) {
  if (isNumber(item.id) && item.id !== 0) {
    return true;
  } 
  invalidEntries++;
  return false; 
}

var arrByID = arr.filter(filterByID);

console.log('Filtered Array\n', arrByID); 
// Filtered Array
// [{ id: 15 }, { id: -1 }, { id: 3 }, { id: 12.2 }]

console.log('Number of Invalid Entries = ', invalidEntries); 
// Number of Invalid Entries = 5
```

- 在数组中搜索

```js
var fruits = ['apple', 'banana', 'grapes', 'mango', 'orange'];

/**
 * Array filters items based on search criteria (query)
 */
function filterItems(query) {
  return fruits.filter(function(el) {
      return el.toLowerCase().indexOf(query.toLowerCase()) > -1;
  })
}

console.log(filterItems('ap')); // ['apple', 'grapes']
console.log(filterItems('an')); // ['banana', 'mango', 'orange']
```



