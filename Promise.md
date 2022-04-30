## Promise

### promise是干什么的

- promise是用来解决javascript中异步操作的问题

### 基本用法

1. 构造一个新的Promise对象

   ```javascript
   let promise = new Promise((resolve, reject) => {
       doTask();//异步处理
       resolve(value);
       //or reject(error)
   })
   ```

   - promise对象创建时，接收一个函数，即你想让它做的事情，这个函数会被立即执行
   - promise有对象中有一个值（下面记作`promise.result`），用来存放task的执行结果，初始值为`undefined`
   - `resolve`和`reject`是javascript本身提供的两个callback函数，当task完成后我们需要调用其中的一个给`promise.result`设值
     - resolve：task执行成功时调用，`resolve(value)`
     - resolve：task执行失败时调用，`reject(error)`
     - 当调用了`resolve`或者`reject`之后，后面的代码将不会被执行（因为task已经完成了）
   - promise的状态
     - `pendeing`：初始状态（promise对象被创建时）
     - `fulfilled`：当`resolve`方法被调用后
     - `rejected`：当`reject`方法被调用后
     - ![image-20220425001129936](D:\git\knowledge\images\promise-status.png)

2. `promise.then`

   ```javascript
   promise.then(
     result => { /* handle a successful result */ },
     error => {/* handle an error */ }
   );
   ```

   - `.then()`方法接受两个函数作为参数
     - onFulfilled：当promise被resolve时调用
     - onRejected：当promise被resolve时调用

3. Promise的链式调用

   - `.then`方法的返回值也是一个一个Promise的对象，因此可以进行链式调用，从而解决回调函数嵌套调用的问题

     ```javascript
     //callback嵌套
     doTask1(a, r1 => {
         doTask2(r1, r2 => {
             doTask3(r2, r3 => {
                 ...
             })
         })
     })
         
     //用Promise改写
     new Promise((resolve, reject) => {
         doTask1();
         resolve(r1);
     }).then(r1 => {
         doTask2();
         return r2;
     }).then(r2 => {
         doTask3();
         return r3;
     })...
     ```

   - 注意，下面这种写法并不是链式调用，而是在同一个promise对象上绑了多个`.then handler`（并不会报错）

     ```javascrtpt
     let promise = new Promise(function(resolve, reject) {
       setTimeout(() => resolve(1), 1000);
     });
     
     promise.then(function(result) {
       alert(result); // 1
       return result * 2;
     });
     
     promise.then(function(result) {
       alert(result); // 1
       return result * 2;
     });
     
     promise.then(function(result) {
       alert(result); // 1
       return result * 2;
     });
     ```

     ![image-20220425005805959](D:\git\knowledge\images\promise_chain.png)

### 异常处理

1. `promise.catch`

   - 当Promise对象在执行中发生异常或者被reject时，进行捕捉and处理

     ```javascript
     let promise = new Promise((resolve, reject) => {
         reject(error);
         //or throw new Error();
     })
     promise.catch(error => errorHandlingFunction)
     ```

   - 当一个promise对象执行发生异常时，会跳到距离他最近的`.catch handler`

     ```javascript
     promise // 异常
     .catch(err1 => {...}) //先跳到这里
     .catch(err2 => {...})
     ```

   - 异常处理完成后，继续执行离他最近的`.then`

     ```javascript
     promise // 异常
     .catch(err1 => {...}) //异常处理OK
     .catch(err2 => {...}) //跳过
     .then() // 继续执行
     ```

   - 如果发生了异常，但代码中没有进行异常处理，那么promise的执行会中断，并且直接在console打出异常信息

2. `promise.finally`

   - 无论promise中的task否成功，都会被执行

     ```javascript
     promise.finally(() => cleanUpFunction)
     ```

   - `.finally()`方法不对`promise.result`进行处理，所以会把当前`promise.result`的内容直接传个下一个handler 

### 其他的一些API

1. Promise.all

   - 比如我们希望同时执行一些task，等这些task全部完成后进行下一步

     ```javascript
     Promise.all([
       new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
       new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2
       new Promise(resolve => setTimeout(() => resolve(3), 1000))  // 3
     ]).then(results => {console.info(results);}); 
     
     //输出结果为[1, 2, 3]
     ```

   - 当所有的promise中的task完成后进入`.then()`

   - `Promise.all`的返回值是一个数组

   - 无论哪一个task先完成，`Promise.all`的返回值的顺序和Promise被定义的顺序相同

   - 当任意一个promise在执行过程中发生Error或者被reject

     - Promise.all的状态被置为`rejected`
     - 没有完成的promise会被继续执行，但是Promise.all不会再继续监视他们的结果

     ```javascript
     Promise.all([
       new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
       new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000)),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
     ]).catch(error => {console.info(error);})   // 输出结果为Error: Error!
     ```

     

2. Promise.allSettled

   - 和`Promise.all`不同，`Promise.allSettled`会等所有的promise执行完毕
   - 返回值同样是一个数组，顺序与promise的定义顺序相同
     - 执行成功：{status:"fulfilled", value:result}
     - 执行失败：{status:"rejected", reason:error}

   ```javascript
   Promise.allSettled([
     new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
     new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000)),
     new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
   ]).then(results => {
   	results.forEach((result, num) => {
         if (result.status == "fulfilled") {
           console.info(`${num}: ${result.value}`);
         }
         if (result.status == "rejected") {
           console.info(`${num}: ${result.reason}`);
         }
       });
   }) 
   
   //0: 1
   //1: Error: Error!
   //2: 3
   ```

3. Promise.race

   - 把最快的完成(Resolved or Rejected)的promise作为结果返回

     ```javascript
     Promise.race([
       new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
       new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000)),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
     ]).then(result => {console.info(result);}); // 1
     ```

4. Promise.any

   - 等待第一个成功完成的promise，并返回它的结果

     ```javascript
     Promise.any([
       new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000)),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
     ]).then(result => {console.info(result);}); // 3
     ```

   - 如果所有的promise都挂了。。。

     ```javascript
     Promise.any([
       new Promise((resolve, reject) => setTimeout(() => reject(new Error("Ouch!")), 1000)),
       new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000))
     ]).catch(error => {
       console.log(error.constructor.name); // AggregateError
       console.log(error.errors[0]); // Error: Ouch!
       console.log(error.errors[1]); // Error: Error!
     });
     
     ```

### Async/await

- Async

  - async关键字用来修饰一个函数，意思是这个函数始终会返回一个Promise

  - 如果函数没有明确的返回一个Promise对象，则会自动包装成一个Promise对象，函数的返回值即为Promise的结果

    ```javascript
    //下面两种写法是等价的
    async function f() {
      return 1;
    }
    
    async function f() {
      return Promise.resolve(1);
    }
    ```

- await

  - await关键字只能用在由async修饰的函数里面

  - await关键字用在一个promise前，那么JavaScript就会等待这个promise执行完，并且拿到它的结果

    ```javascript
    async function f() {
    
      let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("done!"), 1000)
      });
    
      let result = await promise; // wait until the promise resolves
      alert(result); // "done!"
    }
    
    f();
    ```

  - 其实await之后的函数调用就相当于原先写在`then()`handeler里的内容

    ```java
    new Promise((resolve, reject) => {
        setTimeout(() => resolve("done!"), 1000)
      }).then(result => alert(result));
    ```

- 异常处理

  - `try...catch`语句

    ```javascript
    async function f() {
      try {
        let response = await promise;
        doSomething();
      } catch(err) {
        alert(err);
      }
    }
    
    f();
    ```

  - `.catch()`handler

    ```javascript
    async function f() {
      let response = await promise;
      doSomething();
    }
    
    f().catch(err => alert(err));
    ```

    