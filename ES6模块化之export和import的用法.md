### 概念

`export`和`import`是`ES6`模块中的两个命令:`export` 用于从模块中导出实时绑定的函数、对象或原始值，以便其他程序可以通过 `import`使用它们。

ES6中export和import一般的用法有两种

1. 命名导出（Named exports）
2. 默认导出（Default exports）

### 命名导出（Named exports）

就是每一个需要导出的数据类型都要有一个name，统一引入一定要带有`{}`，即便只有一个需要导出的数据类型。**这种写法清爽直观，是推荐的写法**

```javascript
//------ lib.js ------
const sqrt = Math.sqrt;
function square(x) {
    return x * x;
}
function diag(x, y) {
    return sqrt(square(x) + square(y));
}

export {sqrt, square, diag}

//------ main.js ------
import { square, diag } from 'lib';				
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

把export直接加到声明前面就可以省略`{}`

```javascript
//------ lib.js ------
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

//------ main.js ------
import { square, diag } from 'lib';				
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5

```

**无论怎样导出，引入的时候都需要`{}`。**以上是命名导入和命名导出，这样当从不同模块引入的变量名相同的时候，就会冲突，于是有了命名导出和**别名引入（Aliasing named imports）**

```javascript
import {speak as cowSpeak} from './cow.js'
import {speak as goatSpeak} from './goat.js'
```

对应的也有**别名导出**

```javascript
//lib.js
function sayHi(user) {
  alert(`Hello, ${user}!`);
}

function sayBye(user) {
  alert(`Bye, ${user}!`);
}
export {sayHi as hi, sayBye as bye};

//main.js
import * as say from './say.js';

say.hi('John'); // Hello, John!
say.bye('John'); // Bye, John!
```

但是当从每个模块需要引入的方法很多的时候，这种写法就显得十分的繁琐、冗长，例如

```javascript
import {
  speak as cowSpeak,
  eat as cowEat,
  drink as cowDrink
} from './cow.js'

import {
  speak as goatSpeak,
  eat as goatEat,
  drink as goatDrink
} from './goat.js'

cowSpeak() // moo
cowEat() // cow eats
goatSpeak() // baa
goatDrink() // goat drinks
```

于是就有了**命名空间引入（Namespace imports）**

```javascript
import * as cow from './cow.js'
import * as goat from './goat.js'

cow.speak() // moo
goat.speak() // baa
```

------



### 默认导出（Default exports）

默认导出和命名导出相反不需要name，但是一个js文件中只能有一个export default。

```javascript
//------ myFunc.js ------
export default function () { ... };

//------ main1.js ------
import myFunc from 'myFunc';
myFunc();
```

其实这种导出方式可以看成是命名到处的变种，只不过把命名写成了default，此时导入的`name`可以随便写，例如上面的也可以写成`import custom from 'myFunc';`，但是不可以写成`import default from 'myFunc'`或者`import {default} from 'muFunc'`，也就是说使用**默认导出的时候不能使用默认导入**

虽然export default只能有一个，但也可以导出多个方法。

```javascript
export default {
  speak () {
    return 'moo'
  },
  eat () {
    return 'cow eats'
  },
  drink () {
    return 'cow drinks'
  }
}
```

引入与命名空间引入类似

```javascript
import cow from './default-cow.js'
import goat from './default-goat.js'

cow.speak() // moo
goat.speak() // baa
```

------

### 混合导出(Combinations exports)

```javascript
// lib.js
export const myValue = '';
export const MY_CONST = '';
export function myFunc() {
  ...
}
export function* myGeneratorFunc() {
  ...
}
export default class MyClass {
  ...
}

// index.js
import MyClass, { myValue, myFunc } from 'lib';
```

------



### export和import复合使用([Re-exporting / Aggregating](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export#re-exporting_aggregating))

使用库的时候通常会在`index.js`里导入自己写的其他模块，然后再导出给其他人使用，统一管理，例如:

```javascript
export { default as function1,
         function2 } from 'bar.js';
```

与之形成对比的是联合使用导入和导出：

```javascript
import { default as function1,
         function2 } from 'bar.js';
export { function1, function2 };
```

值得注意的是，上述的 `function1` 和 `function2` 并没有引入到当前模块，当前模块相当于一个中转站，只是对 `function1` 和 `function2` 进行转发。

而且还可以对*通过as关键字进行重命名

```javascript
export * as config from "./config.js"
// 等价于
import * as config from "config.js";export { config };

//导入
import  {config} from  "./Es6/index.js"
console.log(config.baseApi)
```

**重点说一下对默认模块的转发**:

```javascript
export {default}  from "./utils.js"
//或者export {default as custom}  from "./utils.js"
//但是不能这样写export default  from "./utils.js"

//导入
import custom from  "./Es6/index.js"//这里custom也可以写成其他的，对应export {default}  from "./utils.js"
//或者
import  {custom} from  "./Es6/index.js" 这里必须是哟个custom接收，对应export {default as custom}  from "./utils.js"
```

导入一个文件不导入任何任何内容，而只是允许模块中的代码

```javascript
import '/modules/my-module.js';
```

这种写法我在前端和原生交互的时候使用过，通常原生把数据返回前端，需要前端有一个全局函数(挂在window上),这个时候我把所有原生的回调方法写在一个文件

------

### 动态导入

在Vue我们写路由的时候导入组件经常使用懒加载就是动态导入

```vue
components: {
    'my-component': () => import('./my-async-component')
  }
```

