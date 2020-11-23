## 迭代器模式

在javascript中有一些api就实现了迭代的效果，那么迭代器模式的核心必然就是迭代的过程，需要注意的是迭代的过程是按一定顺序的，至于具体怎么迭代，是倒序还是顺序，中序，以及迭代的过程中何时暂停，何时添加条件分支都可以由自己规定。

除了javascript给我们提供的一些api,我们可以自行实现迭代的效果,我们选择去实现数组原型上的forEach方法。

```js
Array.prototype.myForEach = function(fn){
  let arr = this;
  for(var i = 0; i < arr.length; i++){
    fn.apply(arr[i], i, arr);
  }
}

```

或者自行实现一个each函数，这允许我们更加灵活的控制迭代中的操作
```js
fucntion each (arr, fn, check){
  for(var i = 0; i < arr.length; i++){
    if(check(arr[i])){
      //添加的判断分支可以终止循环
      break;
    }
    fn.apply(arr[i], i, arr);
  }
}
```


当然ES6在退出了多种数据结构如Map,Set的同时，退出了一套统一的接口，Iterator迭代器，调用迭代器的next方法即按顺序可访问可迭代对象。值得注意的是我们平时的一些操作如for ... of 就是隐式调用的next方法。

我们可以这样获取一个迭代器对象
```js
const arr = [1,2, 3];
let iterator = arr[Symbol.iterator]()

iterator.next();
iterator.next();
iterator.next();
```

迭代器对象是使用迭代器生成函数生成的，而ES6发布后，我们可以使用生成器Generator，调用它就可以得到一个迭代器对象

```js
function *iteratorGenerator(){
  yield '1';
  yield '2';
  yield '3';
}

const iterator = iteratorGenrator();

iterator.next();
iterator.next();
iterator.next();
```


我们可以自己实现一个迭代器生成函数，实际上相当于我们已经完成了一个javascript版本的迭代器模式的实现。

假设我们迭代一个数组
```js
function iteratorGenerator (target) {
  var index = 0,
      len = target.length;

  return {
    next: function () {
      var done = index >= len;
      var value = !done ? target[index++]: undefined;

      return {
        done,
        value
      }
    }
  }
}

```
