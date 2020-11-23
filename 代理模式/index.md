# 代理模式

代理模式核心在于代理对象以和源对象相同或类似的接口提供给使用者，使得其无须区分代理对象和源对象。
代理模式的好处之一在于解耦，我们将核心业务逻辑抽象出来为一个函数(原对象可为一个函数)，而提供给使用者的又是另一个对象，它也提供了相同类型的功能接口，使用者放心的调用该接口，而无须关心源对象。

代理模式分为保护代理，虚拟代理，缓存代理，事件代理。


再次提一句，代理代理，一定要具有相同形式的接口，这使得其与原对象表面看来并没有什么不同。

## 保护代理


一个典型的代理模式的实现就是:ES6出现的Proxy，

Proxy实现了拦截器的功能，即在使用者对目标对象进行访问或其它操作之前进行一些自定义的操作，来对该行为进行限制。

保护代理即可以通过这种方式限制不符合权限的用户的操作。
此处就这么创建了一个代理对象
```js
new Proxy(target, {
  get: function(target, key){
    if(!user.isValidated){
      alert('不符合权限');
      return;
    }

    if(Info.has(key) && user.isValiated){
      return Info.get(key);
    }
    // ... 可能还有其它分支进行拦截
  },
  // 同时也可设置set
})

```


## 事件代理

事件代理不过多提，我们通过代理模式就可一次性实现对子元素的事件监听，由父元素充当代理，对事件进行处理分发。

```js
father.addEventListener('click', function(e){
  if(e.target.tagName === 'button'){
    e.preventDefault();
    // .. 主要逻辑
  }
})

```

## 虚拟代理

预加载:


最好将原对象实现为单一职责制，即setSrc方法仅仅设置原对象src。
```js
class PreLoadImage{

  constructor(imgNode) {
    this.imgNode = imgNode;
  }

  setSrc(imgUrl) {
    this.imgNode.url = imgUrl;
  }
}

```


而代理对象，负责在合适的时机，调用原对象setSrc方法，对外提供一致接口setSrc给用户调用，因此代理对象需要一个虚拟Image实例，通过onload方法，判断什么时机调用对象setSrc,这也是符合单一职责的。

故而

```js
class ProxyImage {
  static URL = 'xxxx';

  constructor(targetImage){
    this.targetImage = targetImage;
  }
  setSrc(targetUrl){
    this.targetImage.setSrc(ProxyImage.URL);
    let virImage = new Image();
    virImage.onload = () => {
      this.targetImage.setSrc(targetUrl);
    }
    // 注意箭头函数保证this根据词法环境指向外界的this，即实例化的代理对象实例
    virImage.src = targetUrl;
  }
}

```


## 缓存代理

在真正调用某个接口，取到某个结果之前，可以通过代理接口，在调用原接口之前查询缓存，如果缓存中有我们需要的结果，则从缓存中取得。

前面的代理都是一个对象代理另一个对象，其实就如我们强调的那样，代理的核心在于提供一致的接口，因此单纯的函数也可以实现代理，而对于函数而言调用是唯一的方式，因此保证了接口的一致性，我们可以使用闭包单纯的返回一个函数，供使用者使用。

原方法
```js
var add = function(){
  let argArr = Array.prototype.slice.call(arguments)
  let res = 0;
  argArr.reduce((res, item) => {
    res += item
  }, 0)
  return res;
}
```

代理该方法,供使用者使用。

```js
var proxyAdd = (function(){
  const cache = {};

  return function(){
    const args = Array.prototype.join.call(arguments, ','); 
    if(args in cache){
      return cache[args]
    }

    // 若缓存中没有
    return cache[args] = add(...arguments);
  }
})()

```