# javascript-craft
整理javascript的各种奇技淫巧

### 1. compose合并函数依次执行 - 来源redux

```javascript
function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```

### 2. 异步请求的多次请求尝试

请求成功后，break语句会停止for循环；
请求失败后，catch捕获错误，进入下次循环。

```javascript
async function trySometimeRequest(time){
  for (let i = 0; i < time; i++) {
    try {
      await fetch('xxx');
      break;
    } catch (error) {}
  }
}
```

### 3. 一行代码实现简单模版引擎

```javascript
function template(tpl, data) {
  return tpl.replace(/{{(.*?)}}/g, (match, key) => data[key.trim()]);
}

// 使用：
template('我是{{name}}，年龄{{age}}，性别{{sex}}', {name: '王海荣', age: 18, sex: '男'}); 
// "我是王海荣，年龄18，性别男"
```

### 4. 数字转为千分位字符

```javascript
/**
 * 数字转为千分位字符
 * @param {Number} num
 * @param {Number} point 保留几位小数，默认2位
 */
function parseToThousandth(num, point = 2) {
  let [sInt, sFloat] = (Number.isInteger(num) ? `${num}` : num.toFixed(point)).split('.');
  sInt = sInt.replace(/\d(?=(\d{3})+$)/g, '$&,');
  return sFloat ? `${sInt}.${sFloat}` : `${sInt}`;
}
```

### 5. 带延时功能的链式调用

```javascript

// 1) 调用方式
new People('whr').sleep(3000).eat('apple').sleep(5000).eat('durian');

// 2) 打印结果
(等待3s)--> 'whr eat apple' -(等待5s)--> 'whr eat durian'

// 3) 以下是代码实现
class People {
  constructor(name) {
    this.name = name;
    this.queue = Promise.resolve();
  }
  eat(food) {
    this.queue = this.queue.then(() => {
      console.log(`${this.name} eat ${food}`)
    });
    return this;
  }
  sleep(time = 0) {
    this.queue = this.queue.then(() => new Promise(res => {
      setTimeout(() => {
        res();
      }, time);
    }))
    return this;
  }
}
```

### 6. 汇总各种异步请求的场景及解决方案

[汇总各种异步请求的场景及解决方案](https://github.com/ronffy/javascript-craft/blob/master/every-async.md)
