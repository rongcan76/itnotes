如何使用一个函数名对应的字符串来调用一个函数？

---

例如定义了一个函数，名字是`fn` :`function fn(){}`，要使用一个`fn`字符串来调用该函数。

假设的使用场景

针对不同行为编写了几个函数：`run(){}` `walk(){}` `fly(){}` ；

获取用户输入的指令（一个字符串`str`），执行相应的操作（如输入`run`执行`run(){}` ）。

---

- `eval('str')`

  eval的各种讨论实在太多，例如[mdn-eval](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/eval)，不再赘述。（我个人尽量不用eval的一个原因是它[废除特性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Deprecated_and_obsolete_features)）

- `setTimeout('str')`或`setInterVal('str')`  其实也使用了eval

- `new Function('str')`  创建了一个匿名的函数

  在[严格模式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)下不可用

- `window['str']`  window是顶级对象（nodejs下是global）

  在[严格模式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)下不可用（该模式下顶级对象成了undefined）



以上方法总有各种不被推荐的理由（理由略:D），好处是写法简单……以下写法“规矩”而“安全”，不过写起来麻烦……

- 分支判断

  ```javascript
  switch(str){
    case 'run':
      run()
      break
    case 'walk':
      walk()
      break
  //等等等等等……
  } 
  ```

- 将函数作为对象的方法（将函数赋值给对象的属性）

  ```javascript
  const fn = {
    run: function() {
      //code
    },
    walk: function() {
      //code
    },
    //codes ....
    fly: function() {
      //code
    }
  }

  fn['str']() //调用
  ```

- ES6 export import

  在稍大的项目进行模块化开发时，比较实用。以下几种写法原理都是调用对象的方法，只是写法有所差异。

  - 如果在被引入模块中将函数变成对象的方法（上一种写法的模块化而已）

    - fn.js

      ```javascript
      const fn = {
        run: function() {
          //code
        },
        walk: function() {
          //code
        },
        //codes ....
        fly: function() {
          //code
        }
      }

      export default fn
      ```

    - index.js 调用fn.js中的方法

      ```javascript
      import fn from './fn'

      fn['walk']()
      ```

  - 如果在被引用模块中使用函数声明或函数表达式，可以在引入时使用as新建一个对象，将被引入的模块内的函数赋值给该对象。

    - fn.js

      ```javascript
      //简略示意
      function walk(){}
      function run(){}
      const fly = function(){}
      export {walk,run,fly}
      ```

    - index.js

      ```javascript
      import * as fn from './fn'
      // fn.js中的fnct
      fn['fly']()
      ```