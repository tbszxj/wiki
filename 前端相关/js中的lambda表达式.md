# js中的labmde表达式

## 一、定义函数的几种方式

1. function方式

   ```javascript
   const fun = function () {
   
   }
   ```

2. 对象字面量中定义

   ```javascript
   const obj = {
       ccc(){
         
       }
     }
   ```

3. ES6中箭头函数方式

   ```javascript
   const ccc = ()=>{
   
     }
   ```

## 二、函数参数和返回值问题

参数

1. 一个参数可以省略小括号

   ```javascript
   const power = num => {
       return num * num;
     }
   ```

2. 两个或多个参数

   ```javascript
   const sum = (num1,num2) => {
       return num1 + num2
     }
   ```

函数中

1. 函数体中只有一行代码,可以省去大括号

   ```javascript
   const sum = (num1,num2) => num1 + num2
   ```

2. 函数体中有多行代码

   ```javascript
   const test = () =>{
       console.log('一行代码');
       console.log('两行代码');
     }
   ```

## 三、箭头函数中this的使用

箭头函数的主要应用场景是作为参数传递给某个函数，作为回调函数使用。

```javascript
//传统回调函数传入
setTimeout(function () {
    console.log(this)
  },1000)
//传入箭头函数作回调函数
setTimeout(()=>{
    console.log(this)
},1000)

//两者的this都指向window对象
```

```javascript
//在对象中使用箭头函数中的this
const obj = {
    aaa() {
      setTimeout(function () {
        console.log(this)//指向window
      })
      setTimeout(()=>{
        console.log(this)//指向obj
      },1000)
    }
  }
  obj.aaa()
```

**结论：箭头函数中的this引用的是最近作用域中的this**