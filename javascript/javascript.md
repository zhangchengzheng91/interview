[返回目录](../README.md)
##### Javascript 相关面试问题
1. #### 继承、原型、原型链
    1.  原型链<br/>
        其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。<br/>
        构造函数、原型和实例的关系：每个构造函数都有一个原型对象(prototype)，原型对象都包含一个指向构造函数的指针(constructor)，而实例都包含一个指向原型对象的内部指针(\__proto__)。
        ```javascript
        function SuperType() {
            this.property = true
        }
        SuperType.prototype.getSuperValue = function() {
            return this.property
        }
        function SubType() {
            this.subproperty = false
        }

        // 继承了 SuperType
        SubType.prototype = new SuperType()
        SubType.prototype.getSubValue = function() {
            return this.subproperty
        }
        var instance = new SubType()
        console.log(instance.getSuperValue()) // true
        console.log(instance.getSubValue()) // false
        instance.__proto__ === SubType.prototype // true
        SubType.prototype.__proto__ === SuperType.prototype // true
        ```
        1.  默认原型
            ```javascript
            SuperType.prototype.__proto__ === Object.prototype // true
            SubType.prototype.__proto__ === Object.prototype // false
            ```
        1.  确定原型和实例的关系<br/>
            1.  instanceof
                ```javascript
                instance instanceof SubType // true
                instance instanceof SuperType // true
                instance instanceof Object // true
                ```
            1.  isPrototypeOf()
                ```javascript
                SubType.prototype.isPrototypeOf(instance) // true
                SuperType.prototype.isPrototypeOf(instance) // true
                Object.prototype.isPrototypeOf(instance) //true
                Object.prototype.isPrototypeOf(SuperType) // true
                Object.prototype.isPrototypeOf(SubType) // true
                SuperType.prototype.isPrototypeOf(SubType) // false
                ```
        1.  谨慎的定义方法
            子类型有时候需要需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法。但不管怎样，给原型添加的代码一定要放在替换原型的语句之后。<br/>
            再通过原型链集成时，不能使用字面量创建原型方法。这样会重写原型链。
        1.  原型链的问题
            1.  原型中包含引用类型值。<br/>
            包含引用类型值的原型属性会被所有实例共享；而这正是为什么要在构造函数中，而不是在原型对象中定义属性[引用类型]的原因。在通过原型来实现继承时，原类型实际上会变成另一个类型的实例。于是，原先的实例属性也就顺利成章的变成了现在的原型属性。
            1.  在创建子类型时，不能向超类型中传递参数。
    1.  借用构造函数<br/>
        在解决原型中包含引用类型值所带来的问题，开发人员开始使用一种叫做**借用构造函数**(constructor stealing)的技术(有时也叫做伪造对象或经典继承)
        ```javascript
        function SuperType() {
            this.colors = ['red', 'blue', 'green']
        }
        SuperType.prototype.getSuperName = function() {
            return 'this is super'
        }
        function SubType() {
            // 继承了 SuperType
            SuperType.call(this)
        }
        SubType.prototype.getSubName = function() {
            return 'this is sub'
        }
        var instance1 = new SubType()
        instance1.colors.push('black')
        instance1.colors // ["red", "blue", "green", "black"]
        instance1.getSuperName // undefined
        instance1.getSubName() // 'this is sub'
        var instance2 = new SubType()
        instance2.colors // ["red", "blue", "green"]
        ```
        借用构造函数的问题：
        如果仅仅是借用构造函数，那么也将无法避免构造函数模式存在的问题---方法都在构造函数中定义，因此函数敷用就无从谈起了。<br/>
        而且，在超类型中定义的方法，对子类型而言也是不可见的，结果所有类型只能使用构造函数模式。
    1.  组合继承(combination inheritance，伪经典继承)<br/>
        指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。<br/>
        使用**原型链**实现对原型属性和方法的继承，通过**借用构造函数**来实现对实例属性的继承。
        ```javascript
        function SuperType(name) {
            this.name = name
            this.colors = ['red', 'blue', 'green']
        }
        SuperType.prototype.sayName = function() {
            return this.name
        }
        function SubType(name, age) {
            // 继承属性
            SuperType.call(this, name) // 第二次调用 SuperType()
            this.age = age
        }
        // 继承方法
        SubType.prototype = new SuperType() // 第一次调用 SuperType()
        SubType.prototype.constructor = SubType
        SubType.prototype.sayAge = function() {
            return this.age
        }
        var instance1 = new SubType('Iron Man', 20)
        instance1.colors.push('black')
        instance1.colors // ["red", "blue", "green", "black"]
        instance1.sayName() // "Iron Man"
        instance1.sayAge() // 20

        var instance2 = new SubType('spider Mane', 16)
        instance2.colors // ["red", "blue", "green"]
        instance2.sayName() // "spider Mane"
        instance2.sayAge() // 16

        // 原型链关系
        instance1.__proto__ === SubType.prototype // true
        SubType.prototype.__proto__ === SuperType.prototype

        instance1 instanceof SubType // true
        instance1 instanceof SuperType // true
        instance1 instanceof Object // true
        ```
        组合继承避免了原型链和借用构造函数的缺陷，融合了他们的优点，成为 Javascript 中最常用的继承模式。
        组合继承最大的问题是无论什么情况下，都会**调用两次**超类型构造函数：一次是在创建子类型原型时，另一次是在子类型构造函数内部。
    1.  原型式继承(Prototypal Inheritance in Javascript)<br/>
        这种方法并没有使用严格意义上的构造函数。借助原型可以基于已有的对象创建新对象，同时还不必创建自定义类型。<br/>
        在object()函数内部，先创建一个临时性的构造函数，然后将传入的对象作为这个构造函数的原型，最后返回了这个临时类型的一个新实例。从本质上将，object()对传入的对象执行了一次浅复制。
        ```javascript
        function object(o) {
            function F() {}
            F.prototype = o
            return new F()
        }
        ```
        ```javascript
        var person = {
            name: 'Nicholas',
            friends: ['Shelby',  'Court', 'Van']
        }

        var anotherPerson = object(person)
        anotherPerson.name = 'Greg'
        anotherPerson.friends.push('Rob')

        var yetAnotherPerson = object(person)
        yetAnotherPerson.name // "Nicholas"
        yetAnotherPerson.friends // ["Shelby", "Court", "Van", "Rob"]
        ```
        ECMAScript 5 通过新增 Object.create()方法规范化了原型式继承。
        ```javascript
        var person = {
            name: 'Nicholas',
            friends: ['Shelby',  'Court', 'Van']
        }
        var anotherPerson = Object.create(person);
        anotherPerson.name = "Greg";
        anotherPerson.friends.push("Rob");

        var yetAnotherPerson = Object.create(person);
        yetAnotherPerson.name // 'Nicholas'
        yetAnotherPerson.friends // ["Shelby", "Court", "Van", "Rob"]
        ```
        在没有必要兴师动众地创建构造函数，而只想让一个对象与另一对象保持类似的情况下，原型式继承完全可以胜任。不过别忘了，包含引用类型值的属性始终都会共享相应的值，就像使用原型模式一样。
    1.  寄生式(parastic)继承<br/>
        寄生式继承是与原型式继承紧密相关的一种思路。寄生式继承的思路与**寄生构造函数**和**工厂模式**类似，即创建一个仅用于封装继承过程的函数，该函数内部已某种方式来增强对象，最后再像真地做了所有工作一样返回对象。
        ```javascript
        function createAnother(original) {
            var clone = Object.create(original) // 通过调用用函数创建一个新对象，存在于__proto__
            clone.sayHi = function() {          // 以某种方式来增强这个对象，私有属性
                console.log('Hi')
            }
            return clone                        // 返回这个对象
        }
        ```
        ```javascript
        var person = {
            name: 'Nocholas',
            firends: ['Shelby', 'Court', 'Van']
        }
        var anotherPerson = createAnother(person)
        anotherPerson.sayHi // 'Hi'
        ```
        在主要考虑对象而不是**自定义类型**和**构造函数**的情况下，寄生式继承也是一种有用的模式。前面示范继承模式时使用的object()函数不是必须的；任何能够返回新对象的函数都适用于此模式。<br/>
        **使用寄生式继承来为对象添加函数，会由于不能做到函数复用而降低效率；这一点与构造函数模式类似**
    1.  寄生组合式继承<br/>
        所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型原型的一个副本而已。
        ```javascript
        function inheritPrototype(subType, superType) {
            // 创建对象，拷贝超类型原型副本
            var prototype = Object.create(superType.prototype)
            // 增强对象，弥补因重写原型而失去的默认的 constructor 属性
            prototype.constructor = subType                     
            // 指定对象，子类型通过原型继承实现继承
            subType.prototype = prototype
        }
        function SuperType(name) {
            this.name = name
            this.colors = ['red', 'blue', 'green']
        }
        SuperType.prototype.sayName = function() {
            return this.name
        }
        function SubType(name, age) {
            SuperType.call(this, name)
            this.age = age
        }
        inheritPrototype(SubType, SuperType)
        SubType.prototype.sayAge = function() {
            return this.age
        }

        var instance1 = new SubType('instance1', 20)
        instance1.sayName() // "instance1"
        instance1.sayAge() // 20
        instance1.colors.push('yellowGreen')
        instance1.colors // ["red", "blue", "green", "yellowGreen"]

        var instance2 = new SubType('instance2', 30)
        instance2.sayName() // "instance2"
        instance2.sayAge() // 30
        instance2.colors // ["red", "blue", "green"]

        instance1 instanceof SubType // true
        instance1 instanceof SuperType // true
        instance1 instanceof Object // true

        SubType.prototype.isPrototypeOf(instance1) // true
        SuperType.prototype.isPrototypeOf(instance1) // true
        Object.prototype.isPrototypeOf(instance1) // true
        ```
        这个例子的高效率体现在他只调用了一次 SuperType 构造函数，并且避免了在 SubType.prototype 上创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isProtoTypeOf()。开发人员认为寄生式组合继承是引用类型最理想的继承范式。
1. #### 闭包，为什么会造成内存泄漏，闭包的使用场景
    闭包是指有权访问另一个函数作用域中的变量的函数<br/>
    ```javascript
    function createComparisonFunction(propertyName) {
        return function(object1, object2) {
            var value1 = object1[propertyName] // propertyName 来自于 createComparisonFunction 的作用域
            var value2 = object2[propertyName] // propertyName
            if (value1 < value2>) {
                return -1
            } else if (value1 > value2) {
                return 1
            } else {
                return 0
            }
        }
    }
    ```
1. #### Javascript 垃圾回收机制
    javaScript 具有自动的垃圾回收机制，也就是说，执行环境会负责管理代码执行过程中使用的内存。
    1.  标记清除(mark-and-sweep)：当变量进入环境(例如，在函数中声明一个变量)时，就将这个变量标记为“进入环境“。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到它们。当变量离开环境时，将其标记为”离开环境“。**最后垃圾回收器会销毁那些带有标记的值并回收它们所占用的内存空间**。
    1.  引用计数(reference conuting)：跟踪记录每个值被引用的次数。如果该值被引用，则引用次数+1；相反，如果包含这个值引用的变量又取了另外一个变量，则这个值的引用次数-1。当垃圾收集器下次再运行时，它会释放那些引用次数为0的值所占的内存。；引用计数存在**循环引用**的问题，导致变量的引用次数永远不会是0<br/>
    ```javascript
    function problem() {
        var objectA = new Object()
        var objectB = new Object()

        objectA.someOtherObject = objectB
        objectB.anotherObject = obbjectA
    }
    ```
    现在大部分,浏览器使用标记清除的垃圾回收策略。<br/>
    手动触发垃圾回收过程：
    ```javascript
    window.CollectGargabe() // IE1
    window.opera.collect() // Opera 7
    ```
    管理内存：一旦数据不再有用，最好通过将其值设置为 null 来释放其引用 --- 这个做法叫做**解除引用**(dereferencing).解除一个值的引用并不意味着自动回收该值所占用的内存。解除引用的真正作用是让值脱离执行环境，以便垃圾收集器下次运行时将其回收。
1. #### 事件流
1. #### 事件委托
1. #### 跨域  
浏览器同源概念；为什么浏览器会有同源策略的限制；跨域的解决方式；
1. #### 如何判断某个对象为某个类（构造函数）的实例
1. #### Javascript 的异步处理方式有哪些。
1. #### 如何发送一个 http 请求，https 的工作原理。https 中间人攻击，证书协议，加密解密内容
1. #### 从页面输入一个链接到页面加载成功的这个过程中发生了什么。
1. #### 前端数据持久化的解决方式以及特点

    | cookies | localStorage | sessionStorage |
    |---|---|---|
    |一般用于辨别用户身份或session跟踪而在用户浏览器的数据，会通过http请求发送给服务端；<br/>分为session cookie和持久性cookie，cookie中设置HttpOnly参数，前端浏览器使用document.cookie是无法读取HttpOnly类型的cookie，被设置HttpOnly的cookie记录只能通过http请求发送到服务端进行读写操作，避免js手动修改cookie，导致安全问题 | 永久存储，同一个域名能够共享，上线 5MB| 与loaclStorage类似，仅作用与当前窗口(当前的浏览器Tab)，窗口关闭是会自动清除 |
    |document.cookie|window.localStorage(localStrage)|window.sessionStorage(sessionStorage)|
    |返回一个字符串，使用split(';')切割为数组，再遍历split('=')处理成一个对象|返回一个对象|返回一个对象|
    |请求携带cookie的方式:<br/>前端:{withCredentials: true}<br/>服务端header("Access-Control-Allow-Credentials: true");<br/>header("Access-Control-Allow-Origin: [hostname]");<br/>fetch([hostname], {credentials: \[include \| omit \| same-origin]})||

1. #### ES6 相关知识点
    1. let, const 作用域问题；let 声明一个引用类型的数据的意义。
    1. 变量的结构赋值，初始值生效的情况。
    1. 箭头函数的this，函数的默认值
    1. Promise 微任务，Promise\[.then|.catch|.reject|.all|.race] 一些 API 的用法。
    1. 手写一个 Promise
    1. Promise 中异步队列，then 的处理顺序
    1. 数组的新方法
    1. 对象的遍历
    1. 对象的深拷贝 | 浅拷贝
    1. 如何复制一个对象 
    1. new 关键字的内部处理，new 关键字做了什么样的操作。构造函数不使用 new 关键字初始化一个实例
1. #### bind, call, apply 三个函数异同。自己动手实现一个 bind 函数。
1. #### requestAnimationFrame
1. #### event loop | microtask
1. #### 如何用 Generator 去实现 async
1. #### 函数的柯里化
1. #### Web 开发当中会遇到哪些缓存问题，如何去优化。
