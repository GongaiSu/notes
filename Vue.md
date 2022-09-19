# Vue

## 生命周期

```js
//vue的生命周期，创建时调用
  created() {
    console.log('home created');
  },
  //销毁调用的生命周期
  destroyed() {
    console.log('home destroyed');
  },
  //活跃时调用的生命周期
  activated() {
    console.log('activated');
  },
  //不活跃时调用的生命周期
  deactivated() {
    console.log('deactivated');
  }
```

* activated和deactivated方法必须是在keep-alive时候才能被调用

## js的高阶函数

### filter：回调函数

*  返回值为boolear类型；
  * 当为true时：函数内部会自动将这次回调的n加入到新的数组中。
  * 当为false时：函数内部会自动过滤掉这次的n；

```javascript
//返回小于40的值
let nums=[10,20,78,30,40,50]

let newNums= nums.filter(function (n){
    return n < 40
})
console.log(newNums);
```

### map函数

* 对数组中的数进行操作

```javascript
//对newNums中的数进行乘2
let new2Nums=newNums.map(function (n){
  return n * 2
})
console.log(new2Nums);
```

### reduce函数

* 对数组中所有的内容进行汇总

```javascript
//将new2Nums中的所有值累加
let total = new2Nums.reduce(function (preValue,n){
  return preValue+n
},0)
```

## 事件修饰符

### Vue中的事件修饰符

​      1.prevent：阻止默认事件（常用）

​      2.stop:阻止事件冒泡（常用）

​      3.once:事件只触发一次（常用）

​      4.capture：使用事件的捕获模式

​      5.self：只有event.target是当前操作的元素时才触发事件

​      6.passive:事件的默认行为立即执行，无需等待事件回调执行完毕

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=devicse-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <style type="text/css">
        *{
            margin-top: 20px;
        }
        a {
            text-decoration: none;
            color: brown;
        }
        .dome{
            background-color: aqua;
            height: 100px;
        }
        .list{
            height: 200px;
            width: 200px;
            background-color: coral;
            overflow: auto;
        }
        li{
            height: 100px;
        }
    </style>
</head>

<body>

    <!-- 
        Vue中的事件修饰符
            1.prevent：阻止默认事件（常用）
            2.stop:阻止事件冒泡（常用）
            3.once:事件只触发一次（常用）
            4.capture：使用事件的捕获模式
            5.self：只有event.target是当前操作的元素时才触发事件
            6.passive:事件的默认行为立即执行，无需等待事件回调执行完毕
     -->

    <!-- 准备一个容器 -->
    <div id="root">
        <h1>你好欢迎学习{{name}}!!!</h1>
        <button v-on:click="showInfo1()">按钮</button>
        <!-- a标签的默认事件是跳转页面，当点击事件触发后，使用prevent修饰符，可以阻止a标签的默认事件 -->
        <a href="https://www.baidu.com" @click.prevent="showInfo2($event)">按钮1</a>
        <!--    2.stop:阻止事件冒泡（常用） -->
        <div class="dome" @click="showInfo2">
            div
            <button @click.stop="showInfo2">按钮</button>
               <!-- 修饰符可以连续写，使用点隔开 -->
            <!-- <a href="https://www.baidu.com" @click.prevent.stop="showInfo2($event)">按钮1</a> -->

        </div>
        <!-- 3.once:事件只触发一次（常用） -->
        <button @click.once="showInfo2">按钮</button>
        <!--capture：使用事件的捕获模式 ，按照事件的捕获顺序执行，从外到里 -->
        <div class="dome" @click.capture="showMsg(1)">
            div1
            <button @click="showMsg(2)">按钮</button>
        </div>
         <!--  self：只有event.target是当前操作的元素时才触发事件 -->
         <div class="dome" @click.self="showInfo2">
            div
            <button @click="showInfo2">按钮</button>
        </div>

        <!--6.passive:事件的默认行为立即执行，无需等待事件回调执行完毕  -->
        <ul @scroll="dome" class="list">
            <li>1</li>
            <li>2</li>
            <li>3</li>
            <li>4</li>
        </ul>

    </div>



    <script type="text/javascript">
        Vue.config.productionTip = false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data: {
                name: 'Vue',
                age: "12"
            },
            methods: {
                showInfo1() {
                    alert('加油学习')
                },
                showInfo2(event) {
                    // console.log(a)
                    console.log(event.target)
                    alert('加油学习')
                },
                showMsg(msg) {
                    // console.log(a)
                    console.log(msg);
                }
                ,dome() {
                    // console.log(a)
                    for (let i = 0; i < 100000; i++) {
                       console.log('@')  
                    }
                    console.log('累坏了');
                }
            }
        })
    </script>
</body>

</html>
```

## 键盘事件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>键盘事件</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>



    <!-- 
        1.Vue中常用的键盘别名
            回车 => enter
            删除 =>delete(捕获"删除"和"退格"键)
            退出 =>esc
            空格 =>space
            换行 =>tab(特殊，必须配合keydown去使用)
            上 =>up
            下 =>down
            左 =>left
            右 =>right

        2.Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转换为kebab-case(短横线命名)

        3.系统修饰符（用法特殊）：ctrl、alt、shift、mete
            (1).配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发
            (2).配合keydown使用：正常触发事件

        4.也可以使用keyCode去指定具体的按键(不推荐)

        5.Vue.config.keyCode.自定义键名 = 键码 ，可以去定制按键别名

        6.按键的键码和键名通过 event.key,event.keyCode 获取：
     -->


    <!-- 准备一个容器 -->
    <div id="root">
        <h1>hello ,{{name}},{{age}}</h1>
        <!-- 小技巧，当指定两个键触发事件，可以连续写两个键，中间用点隔开 -->
        <input type="text" placeholder="按下回车提示输出" @keydown="showInfo">
          <!-- <input type="text" placeholder="按下回车提示输出" @keydown.ctrl.y="showInfo"> -->
    </div>



    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const x = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                name:'Vue',
                age:"12"
            },
            methods:{
                showInfo(event){
                    console.log(event.key,event.keyCode);
                }
            }
        })
    </script>
</body>
</html>
```

## 计算属性

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>姓名案例_计算属性实现</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 准备一个容器 -->
    <div id="root">
        姓：<input type="text" v-model="firstName"><br><br>
        名：<input type="text" v-model="lastName"><br><br>
        全名：<span>{{fullName}}</span>
    </div>

    <!--
        计算属性
            1、定义：要用的属性不存在，用通过已有属性计算得来
            2、原理：底层借助Object.definePorperty方法提供的getter和setter
            3、get函数什么时候执行？
                (1)、初次读取时会执行一次
                (2)、当依赖的数据发生变化时会被再次调用
            4、优势：与methods实现相比，内部有缓存机制，效率更高。调用方便
            5、备注
                1、计算属性最终会出现在vm上，直接读取使用即可
                2、如果计算属性被修改，那必须写set函数去响应修改，且set中引起计算时依赖的数据发生改变
     -->

    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                firstName:'张',
                lastName:"三"
            },
            computed:{
                //完整写法
                fullName:{
                    /*get什么时候调用？
                        1、初次读取计算属性时
                        2、所依赖的数据发生变化时
                    */
                        
                 get(){
                     console.log("get方法被调用");
                     return this.firstName+"-"+this.lastName
                 },
                 set(value){
                     console.log(value);
                     const arr = value.split('-')
                     this.firstName=arr[0]
                     this.lastName=arr[1]
                 }  
                }
                 //简写,当计算属性中只有getter方法才可以简写
                /*fullName(){    
                     console.log("get方法被调用");
                     return this.firstName+"-"+this.lastName
                }*/
            }
        })
    </script>
</body>
</html>
```

## 监视属性

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>天气案例</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        监视属性watch：
            1、当被被监视的属性变化时，回调函数自动调取，进行相关操作
            2、监视的属性必须存在，才能进行监视！！
            3、监视的两种写法
                (1)、new Vue时传入watch配置
                (2)、通过vm.$watch监视
     -->

    <!-- 准备一个容器 -->
    <div id="root">
        <h2>今天天气很{{info}}</h2>
        <button @click="change">切换天气</button>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const x = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                isHost:true
            },
            computed: {
                info(){
                    return this.isHost?'炎热':'凉爽'
                }
            },
            methods: {
                change(){
                    this.isHost=!this.isHost
                }
            },
            watch: {
                isHost:{
                    immediate:true,//初始化时让handler调用一下
                    //handler什么时候调用？当isHost发生改变时
                    handler(newValue,oldValue){
                       console.log('isHost被修改了',newValue,oldValue); 
                    }
                },
                info:{
                    immediate:true,//初始化时让handler调用一下
                    //handler什么时候调用？当isHost发生改变时
                    handler(newValue,oldValue){
                       console.log('info被修改了',newValue,oldValue); 
                    }
                }
            },
        })

    </script>
</body>
</html>
```

### 深度监视

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>天气案例_深度监视 </title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        深度监视：
            1、Vue中watch默认不检测对象内部值的改变(一层)
            2、配置deep：true可以监测对象内部值的改变(多层)
        被指
            (1)、Vue自身可以检测对象内部值的改变，但Vue提供的watch默认不可以
            (2)、使用watch时根据数据的具体结构，决定是否采用深度监视
     -->

    <!-- 准备一个容器 -->
    <div id="root">
        <h3>a的值为:{{number.a}}</h3>
        <button @click="number.a++">a++</button>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                number:{
                    a:1,
                    b:2
                }
            },
            watch: {
                /*
                //监视多级结构中某个属性发变化
                'number.a':{
                    handler(){
                        console.log('a的值发生变化');
                    }
                },
                */
                //监视多级结构中所有属性发变化
                number:{
                    deep:true,
                    handler(){
                        console.log('a的值发生变化');
                    }
                }
            },
        })

    </script>
</body>
</html>
```

## 计算属性和监视属性的区别

1. computed能完成的功能，watch都可以完成
2. watch能完成的功能，computed不一定能完成，例如watch可以进行异步操作
3. 两个重要的小原则
   1. 所有被Vue管理的函数，最好写成普通函数，这样this的指向才是vm或者组件实例对象
   2. 所有不被Vue所管控的函数（定时器的回调函数、ajax的回调函数等，Promise的回调函数），最好携程箭头函数，这样this的指向才是vm或者组件实例对象

## 条件渲染

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>条件渲染</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 
        条件渲染
            1、v-if
                写法：
                    (1)、v-if='表达式'
                    (2)、v-else-if='表达式'
                    (3)、v-else
                使用于：切换频率较低的场景
                特点：不展示的DOM元素直接被移除
                注意：v-if可以和：v-else-if、v-else一起使用，但是要求接口不能被"打断"

            2、v-show
                写法：v-show='表达式'
                适用于：切换频率较高的场景
                特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉
            
            3、备注：使用v-if的时候，元素可能无法获取到，而使用v-show一定能获取到
     -->

    <!-- 准备一个容器 -->
    <div id="root">
        <!-- 使用v-show做条件渲染 -->
        <!-- <h2 v-show=false>欢迎学习{{name}}</h2> -->
        <!-- <h2 v-show=1===1>欢迎学习{{name}}</h2> -->
        <!-- 使用v-if做条件渲染 -->
        <!--  -->
        <h2 v-if=false>欢迎学习{{name}}</h2>
        <!-- <h2 v-if=1===0>欢迎学习{{name}}</h2> -->
    </div>

    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                name:'Vue'
            },
    
        })

    </script>
</body>
</html>
```

## 列表渲染(V-for)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>基本列表</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
    <!-- 
        v-for指令
            1、用于展示列表数据
            2、语法：v-for='(item,index) in xxx' :key='yyy'
            3、可遍历：数组，对象，字符串（用的很少）、指定次数（用的很少）
     -->

    <!-- 准备一个容器 -->
    <div id="root">
        <!-- 遍历数组 -->
        <ul>
            <li v-for="(person,index) of persons" :key="index">
                {{person.name}}-{{person.age}}
            </li>
        </ul>
        <!-- 遍历对象 -->
        <ul>
            <li v-for="(value,key) of car" :key="key">
                {{value}}
            </li>
        </ul>
        <!-- 遍历字符串 -->
        <ul>
            <li v-for="(value,key) of str" :key="key">
                {{value}}
            </li>
        </ul>
        <!-- 遍历次数 -->
        <ul>
            <li v-for="(value,key) of 10" :key="key">
                {{value}}
            </li>
        </ul>

    </div>



    <script type="text/javascript">
        Vue.config.productionTip = false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data: {
                persons: [
                    { id: 001, name: '张三', age: 18 },
                    { id: 002, name: '李四', age: 19 },
                    { id: 003, name: '王五', age: 20 },
                ],
                car: {
                    name: '奔驰大G',
                    price: '200万',
                    color: '黑色'
                },
                str: 'hello'
            }
        })
    </script>
</body>

</html>
```

### key的原理

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>key的原理</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
    <!-- 
				面试题：react、vue中的key有什么作用？（key的内部原理）
						
						1. 虚拟DOM中key的作用：
										key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】, 
										随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：
										
						2.对比规则：
									(1).旧虚拟DOM中找到了与新虚拟DOM相同的key：
												①.若虚拟DOM中内容没变, 直接使用之前的真实DOM！
												②.若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM。

									(2).旧虚拟DOM中未找到与新虚拟DOM相同的key
												创建新的真实DOM，随后渲染到到页面。
												
						3. 用index作为key可能会引发的问题：
											1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作:
															会产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低。

											2. 如果结构中还包含输入类的DOM：
															会产生错误DOM更新 ==> 界面有问题。

						4. 开发中如何选择key?:
											1.最好使用每条数据的唯一标识作为key, 比如id、手机号、身份证号、学号等唯一值。
											2.如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，
												使用index作为key是没有问题的。
		-->

    <!-- 准备一个容器 -->
    <div id="root">

        <button @click.once="add">点击添加赵六</button>
        <!-- 遍历数组 -->
        <ul>
            <li v-for="(person,index) of persons" :key="person.id">
                {{person.name}}-{{person.age}}
                <input type="text">
            </li>
        </ul>

    </div>



    <script type="text/javascript">
        Vue.config.productionTip = false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data: {
                persons: [
                    { id: 001, name: '张三', age: 18 },
                    { id: 002, name: '李四', age: 19 },
                    { id: 003, name: '王五', age: 20 },
                ],
            },
            methods: {
                add(){
                    const p = { id: 004, name: '赵六', age: 21 }
                    this.persons.unshift(p)
                }
            },
        })
    </script>
</body>

</html>
```

### 列表过滤

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表过滤</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
    

    <!-- 准备一个容器 -->
    <div id="root">
        <input type="text" v-model="keyWord">
        <!-- 遍历数组 -->
        <ul>
            <li v-for="(person,index) of filPersons" :key="index">
                {{person.name}}-{{person.age}}
            </li>
        </ul>

    </div>



    <script type="text/javascript">
        Vue.config.productionTip = false //阻止vue在启动时生成生产提示

        //创建Vue实例
        //使用监视属性实现过滤
       /* const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data: {
                keyWord:'',
                persons: [
                    { id: 001, name: '马冬梅', age: 18 },
                    { id: 002, name: '周冬雨', age: 19 },
                    { id: 003, name: '周杰伦', age: 20 },
                    { id: 004, name: '温兆伦', age: 21 },
                ],
                filPersons:[]
            },
            watch: {
                keyWord:{
                    immediate:true,
                    handler(val){
                        this.filPersons = this.persons.filter((p)=>{
                            return p.name.indexOf(this.keyWord)!==-1
                        })
                    }
                }
            },
        })*/


        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data: {
                keyWord:'',
                persons: [
                    { id: 001, name: '马冬梅', age: 18 },
                    { id: 002, name: '周冬雨', age: 19 },
                    { id: 003, name: '周杰伦', age: 20 },
                    { id: 004, name: '温兆伦', age: 21 },
                ],
            },
            computed: {
                filPersons(){
                   return this.persons.filter((p)=>{
                      return p.name.indexOf(this.keyWord)!==-1
                    })
                }
            },
        })
    </script>
</body>

</html>
```

### 列表排序

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表排序</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
    

    <!-- 准备一个容器 -->
    <div id="root">
        <input type="text" v-model="keyWord">
        <button @click="sortType = 2">年龄升序</button>
        <button @click="sortType = 1">年龄降序</button>
        <button @click="sortType= 0">原顺序</button>
        <!-- 遍历数组 -->
        <ul>
            <li v-for="(person,index) of filPersons" :key="person.id">
                {{person.name}}-{{person.age}}
            </li>
        </ul>

    </div>



    <script type="text/javascript">
        Vue.config.productionTip = false //阻止vue在启动时生成生产提示

        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data: {
                keyWord:'',
                sortType:0,//0原顺序，1降序，2升序
                persons: [
                    { id: 001, name: '马冬梅', age: 18 },
                    { id: 002, name: '周冬雨', age: 16 },
                    { id: 003, name: '周杰伦', age: 29 },
                    { id: 004, name: '温兆伦', age: 25 },
                ],
            },
            computed: {
                filPersons(){
                   const arr = this.persons.filter((p)=>{
                      return p.name.indexOf(this.keyWord)!==-1
                    })
                    if(this.sortType){
                        arr.sort((a,b)=>{
                            return this.sortType==1 ? b.age-a.age : a.age-b.age
                        })
                    }
                    return arr
                }
            },
        })
    </script>
</body>

</html>
```

### 数据监测

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表排序</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
    
    <!--
			Vue监视数据的原理：
				1. vue会监视data中所有层次的数据。

				2. 如何监测对象中的数据？
								通过setter实现监视，且要在new Vue时就传入要监测的数据。
									(1).对象中后追加的属性，Vue默认不做响应式处理
									(2).如需给后添加的属性做响应式，请使用如下API：
													Vue.set(target，propertyName/index，value) 或 
													vm.$set(target，propertyName/index，value)

				3. 如何监测数组中的数据？
									通过包裹数组更新元素的方法实现，本质就是做了两件事：
										(1).调用原生对应的方法对数组进行更新。
										(2).重新解析模板，进而更新页面。

				4.在Vue修改数组中的某个元素一定要用如下方法：
							1.使用这些API:push()、pop()、shift()、unshift()、splice()、sort()、reverse()
							2.Vue.set() 或 vm.$set()
				
				特别注意：Vue.set() 和 vm.$set() 不能给vm 或 vm的根数据对象 添加属性！！！
		-->

    <!-- 准备一个容器 -->
    <div id="root">
        <h1>学生信息</h1>
        <button @click="student.age++">年龄+1岁</button>
        <button @click.once="addSex">添加性别属性，默认值：男</button>
        <button @click="addFirstFriend">在列表首位添加一个朋友</button>
        <button @click="updateFirstFriend">修改第一个朋友的名字为：张三</button>
        <button @click="addHobby">添加一个爱好</button>
        <button @click="updateHobby">修改第一个爱好为：开车</button>





        <h3>姓名：{{student.name}}</h3>
        <h3>年龄：{{student.age}}</h3>
        <h3 v-if="student.sex">性别：{{student.sex}}</h3>
        <h3>爱好：</h3>
        <ul>
            <li v-for="(h,index) of student.hobby" ::key="index">
                {{h}}
            </li>
        </ul>
        <h3>朋友</h3>
        <ul>
            <li v-for="(f,index) of student.friends" ::key="index">
                {{f.name}}--{{f.age}}
            </li>
        </ul>

    </div>



    <script type="text/javascript">
        Vue.config.productionTip = false //阻止vue在启动时生成生产提示

        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data: {
                student:{
                    name:'tom',
                    age:18,
                    hobby:['抽烟','喝酒','烫头'],
                    friends:[
                        {name:'jerry',age:35},
                        {name:'tony',age:36}
                    ]
                }
            },
            methods: {
                addSex(){
                    this.$set(this.student,'sex','男')
                },
                addFirstFriend(){
                    this.student.friends.unshift({name:'jack',age:37})
                },
                updateFirstFriend(){
                    // this.$set(this.student.friends[0],'name','张三')
                    this.student.friends[0].name='张三'
                },
                addHobby(){
                    this.student.hobby.push('打游戏')
                },
                updateHobby(){
                    // this.$set(this.student.hobby,0,'开车')
                    this.student.hobby.splice(0,1,'开车')
                }
            },
            
        })
    </script>
</body>

</html>
```

## 表单数据收集(v-model)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>收集表单数据</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>


    <!-- 
			收集表单数据：
					若：<input type="text"/>，则v-model收集的是value值，用户输入的就是value值。
					若：<input type="radio"/>，则v-model收集的是value值，且要给标签配置value值。
					若：<input type="checkbox"/>
							1.没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
							2.配置input的value属性:
									(1)v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
									(2)v-model的初始值是数组，那么收集的的就是value组成的数组
					备注：v-model的三个修饰符：
									lazy：失去焦点再收集数据
									number：输入字符串转为有效的数字
									trim：输入首尾空格过滤
		-->

    <!-- 准备一个容器 -->
    <div id="root">
        <form  @submit.prevent="dome">
            账号：<input type="text" name="" id="" v-model="accout"><br>
            密码：<input type="password" name="" id="" v-model="password"><br>
            性别：
            男<input type="radio" name="sex" id="" value="1" v-model="sex">
            女<input type="radio" name="sex" id="" value="0" v-model="sex"><br>
            爱好：
            学习 <input type="checkbox" name="" id="" value="xx" v-model="hobby">
            打游戏 <input type="checkbox" name="" id="" value="dyx" v-model="hobby">
            吃饭 <input type="checkbox" name="" id="" value="cf" v-model="hobby"><br>
            所属校区
            <select name="" id="" v-model="student">
                <option value="">请选择校区</option>
                <option value="bj" >北京</option>
                <option value="sh" >上海</option>
                <option value="wh" >武汉</option>
            </select><br>
            其他信息
            <textarea name="" id="" cols="30" rows="10" v-model="textarea"></textarea><br>
            <input type="checkbox" name="" id="" v-model="agree"> 阅读并接受协议<a href="">《用户协议》</a><br>
            <button>提交</button>
        </form>
    </div>



    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                accout:'',
                password:'',
                sex:'0',
                hobby:[],
                student:'',
                textarea:'',
                agree:false
            },
            methods: {
                dome(){
                    console.log(JSON.stringify(this.$data));
                } 
            },
        })
    </script>
</body>
</html>
```

## 过滤器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>过滤器</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <script type="text/javascript" src="../js/dayjs.min.js"></script>
</head>
<body>


    <!-- 
			过滤器：
				定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）。
				语法：
						1.注册过滤器：Vue.filter(name,callback) 或 new Vue{filters:{}}
						2.使用过滤器：{{ xxx | 过滤器名}}  或  v-bind:属性 = "xxx | 过滤器名"
				备注：
						1.过滤器也可以接收额外参数、多个过滤器也可以串联
						2.并没有改变原本的数据, 是产生新的对应的数据
		-->

    <!-- 准备一个容器 -->
    <div id="root">
       <h2>显示格式化后的时间</h2>
       <h3>现在是{{formatTime}}</h3>
       <h3>现在是{{formatData()}}</h3>
       <h3>现在是{{time | timeFormater}}</h3>
       <h3>现在是{{time | timeFormater('YYYY_MM_DD') | mySlice}}</h3>
    </div>



    <script type="text/javascript"> 
        Vue.config.productionTip =false
        //全局过滤器
        Vue.filter('mySlice',function(value){
            return  value.slice(0,4)
        }) //阻止vue在启动时生成生产提示

        //创建Vue实例
        const x = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                time:1654094806517
            },
            computed: {
                formatTime(){
                    return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
                }
            },
            methods: {
                formatData(){
                    return  dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
                }
            },
            //局部过滤器
            filters:{
                timeFormater(time,str='YYYY-MM-DD HH:mm:ss'){
                    return  dayjs(time).format(str) 
                },
            }
        })
    </script>
</body>
</html>
```

## v-text

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        v-text指令：
            1、作用：向其所在的节点中渲染文本内容
            2、与插值语法的区别：v-text会替换掉节点中的内容，而插值语法不会
     -->

    <!-- 准备一个容器 -->
    <div id="root">
        <h1>hello ,{{name}}</h1>
        <h1 v-text="name"> </h1>
    </div>



    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                name:'Vue'
            }
        })
    </script>
</body>
</html>
```

## V-html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
				v-html指令：
						1.作用：向指定节点中渲染包含html结构的内容。
						2.与插值语法的区别：
									(1).v-html会替换掉节点中所有的内容，{{xx}}则不会。
									(2).v-html可以识别html结构。
						3.严重注意：v-html有安全性问题！！！！
									(1).在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。
									(2).一定要在可信的内容上使用v-html，永不要用在用户提交的内容上！
		-->

    <!-- 准备一个容器 -->
    <div id="root">
        <h1>hello ,{{name}}</h1>
        <span v-html="str"> </span>
    </div>



    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                name:'Vue',
                str:'<h1>hello ,Vue</h1>'
            }
        })
    </script>
</body>
</html>
```

## v-cloak

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <style>
        [v-cloak] {
            display: none;
        }
    </style>
</head>
<body>
    <!-- 
				v-cloak指令（没有值）：
						1.本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性。
						2.使用css配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题。
		-->
   

    <!-- 准备一个容器 -->
    <div id="root">
        <h1 v-cloak>hello ,{{name}}</h1> 
    </div>



    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                name:'Vue',
               
            }
        })
    </script>
</body>
</html>
```

## v-once

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
        v-once指令：
            1、v-once所在节点在初次动态渲染后，就视为静态节点，不再收到任何变化。
            2、以后数据的改变不会引发v-once所在结构的更新，可以用于优化性能
     -->
    

    <!-- 准备一个容器 -->
    <div id="root">
        <h1 v-once>年龄是{{n}}</h1> 
        <h1 >年龄是{{n}}</h1> 
        <button @click="n++">点击</button>
    </div>



    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                n:1,   
            }
        })
    </script>
</body>
</html>
```

## v-pre

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
   <!-- 
       v-pre指令：
            1、跳过其所在节点的编译过程
            2、可以利用v-pre指令来提前渲染某些节点，提高性能
    -->
    

    <!-- 准备一个容器 -->
    <div id="root">
        <h1 v-pre>你好vue</h1> 
        <h1 v-pre>年龄是{{n}}</h1> 
        <button v-pre @click="n++">点击</button>
    </div>



    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                n:1,   
            }
        })
    </script>
</body>
</html>
```

## 自定义指令

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 
        需求1：定义一个v-big指令，和v-text功能类似，但会把绑定的数值放大10倍。
		需求2：定义一个v-fbind指令，和v-bind功能类似，但可以让其所绑定的input元素默认获取焦点。
				自定义指令总结：
						一、定义语法：
									(1).局部指令：
												new Vue({															new Vue({
													directives:{指令名:配置对象}   或   		directives{指令名:回调函数}
												}) 																		})
									(2).全局指令：
													Vue.directive(指令名,配置对象) 或   Vue.directive(指令名,回调函数)

						二、配置对象中常用的3个回调：
									(1).bind：指令与元素成功绑定时调用。
									(2).inserted：指令所在元素被插入页面时调用。
									(3).update：指令所在模板结构被重新解析时调用。

						三、备注：
									1.指令定义时不加v-，但使用时要加v-；
									2.指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名。
		-->
    <!-- 准备一个容器 -->
    <div id="root">
        <h2 >当前n值为<span v-text="n"></span></h2> 
        <h2>放大10倍后的n值是 <span v-big="n"></span></h2>
        <input type="text" v-fbind:value="n">
        <button @click="n++">点击n++</button>
    </div>
 


    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //全局自定义指令
        Vue.directive('fbind',{
            bind(el,binding){
                console.log('bind');
                el.value = binding.value 
            },
            // 指令所在元素被插入页面时调用
            inserted(el){
                console.log('inserted');
                el.focus()
            },
            // 指令所在模板结构被重新解析时调用
            update(el,binding){
                el.value = binding.value
                console.log('update');
                el.focus()
            }
        })

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                n:1,
                name:'Vue'   
            },
            directives:{
                big(el,binding){
                    el.innerHTML = binding.value * 10
                },
                //局部指令
                // fbind:{
                //     // 指令与元素成功绑定时调用（一上来）
                //     bind(el,binding){
                //         console.log('bind');
                //         el.value = binding.value
                        
                //     },
                //     // 指令所在元素被插入页面时调用
                //     inserted(el){
                //         console.log('inserted');
                //         el.focus()
                //     },
                //     // 指令所在模板结构被重新解析时调用
                //     update(el,binding){
                //         el.value = binding.value
                //         console.log('update');
                //         el.focus()
                //     }
                // }
            }
        })
    </script>
</body>
</html>
```

## 生命周期函数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 
        生命周期：
            1、又名：生命周期回调函数、生命周期函数、生命周期钩子
            2、是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数
            3、生命周期函数名字不可更改，但函数中的具体内容是程序员根据需求编写的
            4、生命周期函数中的this指向Vue实例，或组件实例对象
            5、生命周期函数的执行顺序：
                1、beforeCreate
                2、created
                3、beforeMount
                4、mounted
                5、beforeUpdate
                6、updated
                7、beforeDestroy
                8、destroyed
     -->

    <!-- 准备一个容器 -->
    <div id="root">
        <h2 :style="{opacity}">Vue</h2> 
    </div>
 


    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

    

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                opacity:1  
            },
            methods: {
            },
            //Vue完成模板的解析并把初始的真实DOM元素插入到页面后，调用mounted
            mounted() {
                setInterval(() => {
                    this.opacity -= 0.01
                    if(this.opacity<=0){
                        this.opacity = 1
                    }
                }, 16)
            },
            
        })
    </script>
</body>
</html>
```

### 生命周期图

![生命周期](Vue.assets/生命周期.png)

### 生命周期总结

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初始Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 
				常用的生命周期钩子：
						1.mounted: 发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。
						2.beforeDestroy: 清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】。

				关于销毁Vue实例
						1.销毁后借助Vue开发者工具看不到任何信息。
						2.销毁后自定义事件会失效，但原生DOM事件依然有效。
						3.一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了。
		-->

    <!-- 准备一个容器 -->
    <div id="root">
        <h2 :style="{opacity}">Vue</h2> 
        <button @click="stop">停止</button>
    </div>
 


    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

    

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                opacity:1  
            },
            methods: {
                stop(){
                    this.$destroy();
                }
            },
            //Vue完成模板的解析并把初始的真实DOM元素插入到页面后，调用mounted
            mounted() {
                this.timer = setInterval(() => {
                    this.opacity -= 0.01
                    if(this.opacity<=0){
                        this.opacity = 1
                    }
                }, 16)
            },
            beforeDestroy() {
                clearInterval(this.timer)
            },
            
        })
    </script>
</body>
</html>
```



## V-Model表单绑定(双向绑定)

### 基本使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <input type="text" :value="message" @input="message=$event.target.value">
    {{message}}
  </div>

  <script>
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {
      message:"zhangsan"
    },
    methods: {
    }
   });
  </script>
</body>

</html>
```

* 原理相当于把v-on和v-bind进行了语法糖
* v-model绑定同一个属性在radio中是互斥的，不需要添加name属性。

* v-model:checkbox

  * 单选框 

  * ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>Title</title>
    </head>
    <body>
      <div id ="app">
    <!--      <lable for="consent">-->
    <!--        <input type="checkbox" id="consent" v-model="isConsent" >同意协议-->
    <!--      </lable>-->
    <!--    <h2>{{isConsent}}</h2>-->
    <!--    <button :disabled="!isConsent">下一步</button>-->
      </div>
        <script src="../js/vue.js"></script>
        <script>
         const vm = new Vue({
          el: '#app',
          data: {
            isConsent:false 
          },
          methods: {
          }
         });
        </script>
    </body>
    </html>
    ```

  * 复选框

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>Title</title>
    </head>
    <body>
      <div id ="app">
        <input type="checkbox" value="篮球" v-model="hobbies">篮球
        <input type="checkbox" value="足球" v-model="hobbies">足球
        <input type="checkbox" value="羽毛球" v-model="hobbies">羽毛球
        <input type="checkbox" value="网球" v-model="hobbies">网球
        <h2>{{hobbies}}</h2>
      </div>
        <script src="../js/vue.js"></script>
        <script>
         const vm = new Vue({
          el: '#app',
          data: {
            hobbies:[]
          },
          methods: {
          }
         });
        </script>
    </body>
    </html>
    ```

* 值绑定：不要像以前一样进行直接的值绑定，而是写一个数组进行绑定，进行循环遍历。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
  <div id ="app">
    <label v-for="item in originHobbies" :for="item">
      <input type="checkbox" :id="item" :value="item" v-model="hobbies">{{item}}
    </label>
    <h2>{{hobbies}}</h2>
    </div>
    <script src="../js/vue.js"></script>
    <script>
     const vm = new Vue({
      el: '#app',
      data: {
        message:'',
        hobbies:[],
        originHobbies:["篮球","羽毛球","乒乓球","网球"]
      },
      methods: {
      }
     });
    </script>
</body>
</html>
```

* v-model的修饰符：
  * lazy：当数据失去焦点或者按下回车才会更新
  * number：可以时输入框中的内容自动转换为数字类型；如果添加这个修饰符，默认为String
  * trim：可以过滤掉内容两边的空格。

## 组件

### 基本使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>非单文件组件的基本使用</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 
			Vue中使用组件的三大步骤：
					一、定义组件(创建组件)
					二、注册组件
					三、使用组件(写组件标签)

			一、如何定义一个组件？
						使用Vue.extend(options)创建，其中options和new Vue(options)时传入的那个options几乎一样，但也有点区别；
						区别如下：
								1.el不要写，为什么？ ——— 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
								2.data必须写成函数，为什么？ ———— 避免组件被复用时，数据存在引用关系。
						备注：使用template可以配置组件结构。

			二、如何注册组件？
							1.局部注册：靠new Vue的时候传入components选项
							2.全局注册：靠Vue.component('组件名',组件)

			三、编写组件标签：
							<school></school>
		-->


    <!-- 准备一个容器 -->
    <div id="root">
        <!-- 使用组件 -->
        <school></school><hr>
        <student></student>
    </div>
 


    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //定义一个组件
        const school=Vue.extend({
            template: `
            <div>
                <h2>{{schoolName}}</h2>
                <h2>{{address}}</h2>
            </div>
            `,
            data(){
                return {
                    schoolName:'清华大学',
                    address:'北京市海淀区',

                }
            }
        })
        const student= Vue.extend({
            template:`
            <div>
                <h2>{{studentlName}}</h2>
                <h2>{{age}}</h2>
            </div>
            `,
            data(){
                return{
                    studentlName:'张三',
                    age:24
                }
            }


        })
        //全局组件
        // Vue.component('school',school)

        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                opacity:1  
            },
            //注册组件
            components:{
                school,
                student
            }
           
            
        })
    </script>
</body>
</html>
```

### VueComponent

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VueComponent</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 
			关于VueComponent：
						1.school组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的。

						2.我们只需要写<school/>或<school></school>，Vue解析时会帮我们创建school组件的实例对象，
							即Vue帮我们执行的：new VueComponent(options)。

						3.特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent！！！！

						4.关于this指向：
								(1).组件配置中：
											data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【VueComponent实例对象】。
								(2).new Vue(options)配置中：
											data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【Vue实例对象】。

						5.VueComponent的实例对象，以后简称vc（也可称之为：组件实例对象）。
							Vue的实例对象，以后简称vm。
		-->

    <!-- 准备一个容器 -->
    <div id="root">
        <!-- 使用组件 -->
        <school>
        </school><hr>
       
    </div>
 
    <script type="text/javascript">
        Vue.config.productionTip =false //阻止vue在启动时生成生产提示

        //定义一个组件
        const school=Vue.extend({
            template: `
            <div>
                <h2>{{schoolName}}</h2>
                <h2>{{address}}</h2>
                <button @click="showName">显示名字</button>
            </div>
            `,
            data(){ 
                return {
                    schoolName:'清华大学',
                    address:'北京市海淀区',

                }
            } ,
            methods: {
                showName(){
                    console.log(this);
                    alert(this.schoolName)
                }
            },
        })
        //简写
       
        
        //创建Vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
            data:{
                opacity:1  
            },
            //注册组件
            components:{
                school,
            }
           
            
        })
    </script>
</body>
</html>
```

### 一个重要的内置关系

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>一个重要的内置关系</title>
		<!-- 引入Vue -->
		<script type="text/javascript" src="../js/vue.js"></script>
	</head>
	<body>
		<!-- 
				1.一个重要的内置关系：VueComponent.prototype.__proto__ === Vue.prototype
				2.为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue原型上的属性、方法。
		-->
		<!-- 准备好一个容器-->
		<div id="root">
			<school></school>
		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
		Vue.prototype.x = 99

		//定义school组件
		const school = Vue.extend({
			name:'school',
			template:`
				<div>
					<h2>学校名称：{{name}}</h2>	
					<h2>学校地址：{{address}}</h2>	
					<button @click="showX">点我输出x</button>
				</div>
			`,
			data(){
				return {
					name:'尚硅谷',
					address:'北京'
				}
			},
			methods: {
				showX(){
					console.log(this.x)
				}
			},
		})

		//创建一个vm
		const vm = new Vue({
			el:'#root',
			data:{
				msg:'你好'
			},
			components:{school}
		})

		
		//定义一个构造函数
		/* function Demo(){
			this.a = 1
			this.b = 2
		}
		//创建一个Demo的实例对象
		const d = new Demo()

		console.log(Demo.prototype) //显示原型属性

		console.log(d.__proto__) //隐式原型属性

		console.log(Demo.prototype === d.__proto__)

		//程序员通过显示原型属性操作原型对象，追加一个x属性，值为99
		Demo.prototype.x = 99

		console.log('@',d) */

	</script>
</html>
```

![image-20220607224002461](Vue.assets/image-20220607224002461.png)

## 组件化

* 将一个个不同的功能抽出为一个个组建，最后将一个个组件组合在一起。每一个组件又可以分为多个组件。 

* extend：创建组件构造器

  ```html
  //1、创建组件构造器
     const cnpC = Vue.extend({
       template:`
       <div>
        <h1>你好</h1>
        <p>1111</p>
        <p>2222</p>
      </div>
      `
     })
  ```

* component；注册组件(全局组件)

  ```html
  //2、注册组建
     Vue.component("my-con",cnpC)
  ```

* 使用组件：在vue实例中使用组件，使用方法就是以第一参数。

* 全局组件：在所有的vue实例中都可以使用。

* 局部组件：在单个vue实例中进行注册，只能在注册的vue实例中使用，无法在其他实例中使用

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../js/vue.js"></script>
  </head>
  
  <body>
    <div id ="app">
      {{message}} 
      <myv></myv>
    </div>
  
    <script>
     //创建Vue实例,得到 ViewMode
     const v1 = Vue.extend({
       template:`
       <div>
        <h1>111</h1>
      </div>
       `
     })
     var vm = new Vue({
      el: '#app',
      data: {
        message:"你好"
      },
      methods: {},
      // 局部组件
      components: {
        myv:v1
      }
     });
    </script>
  </body>
  
  </html>
  ```


### 父组件和自组件

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app">
    {{message}}
    <cnp1></cnp1>
  </div>

  <script>
  //  创建Vue实例,得到 ViewModel
    //子组件
   const cnpC2 = Vue.extend({
   template:`
   <div>
    <h1>你好1www</h1>
    <p>1111</p>
    <p>2222</p>
  </div>
  `
  // components:{
    // cnp1:cnpC1
  // }
 })
   //父组件
 const cnpC1 = Vue.extend({
   template:`
   <div>
    <h1>你好weqwe</h1>
    <p>1111</p>
    <p>2222</p>
    <cnp2></cnp2>
  </div>`,
  components:{
    cnp2:cnpC2
  }
 })
  //  Vue.component("my-con",cnpC)

   const vm = new Vue({
    el: '#app',
    data: {
      message:"niaho "
    },
    methods: {},
    components: {
      // cnp2:cnpC2,
      cnp1:cnpC1
    }
   });
  </script>
</body>

</html>
```

### 组建的语法糖

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app">
  <cpn1></cpn1>  
  </div>

  <script>
   //创建Vue实例,得到 ViewModel
    //在调用component方法时内部会调用extend方法
   Vue.component('cpn1',{
     template:`
     <div>
      <h1>你好</h1>
      </div>
     `
   })
   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {},
    // components: {
      // 'cpn1':{
        // template:`
          // <div>
          //  <h1>你好</h1>
          // </div>
          // `
      // }
    // }
   });
  </script>
</body>
</html>
```

### 组件分离写法

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <cpn1></cpn1>
  </div>
<!--script方法分离  -->
<script type="text/x-template" id="cpn">
  <div>
    <h1>111</h1>
  </div>
</script>
<!-- template分离方法 -->
<!-- <template id="cpn"> -->
  <!-- <div> -->
    <!-- <h1>111</h1> -->
  <!-- </div> -->
<!-- </template> -->
  <script>
   //创建Vue实例,得到 ViewModel
   Vue.component('cpn1',{
     template:'#cpn'
   })

   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {}
   });
  </script>
</body>

</html>
```

### 组件访问Vue实例中的数据

* 组件中无法访问Vue实例中的数据，在组件内部有data属性可以存放组件中的数据，但是这个data属性不是对象类型，相当于一个函数，返回的是一个对象。
* Q&A
  * Q：为什么必须是一个函数；
  * A：每一个组件之间相互独立，为了保证组件这间互不影响。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <cpn1></cpn1>
  </div>
<template id="cpn">
  <div>
    <h1>111</h1>
    <h2>{{title}}</h2>
    <span>{{name}}</span>
    <p v-for="item in hobbies">{{item}}</p>
  </div>
</template>
  <script>
   //创建Vue实例,得到 ViewModel
   Vue.component('cpn1',{
     template:'#cpn',
     data() {
       return {
         title:'abc',
         name: '张三',
         hobbies:['网球','篮球','网球']
       }
     }
   })

   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {}
   });
  </script>
</body>
</html>
```

### 父子组件的通信

* 通过props向子组件传递数据
* 通过实践向父组件发送消息

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <!-- <cpn :cmessage="message"></cpn> -->
    <cpn :cmessage="message"></cpn>
  </div>



  <template id="cpn">
    <div>
      <h1>{{cmessage}}</h1>
    </div>
  </template>
  <script>
    const cpn = {
      template:'#cpn',
      // props: ["cmessage"],
      props:{
        cmessage:{
          type:String,//类型
          default:"aaaaaa",//默认值
          required:true //必传值
        },
        cmove:{
          type:Array,
          default(){
            return []
          }
          // Invalid default value for prop "cmove": Props with type Object/Array must use a factory function to return the default value.:
          // prop“cmove”的默认值无效：具有类型对象/数组的道具必须使用出厂函数返回默认值
        }
      },
      data () {
        return {
          
        }
      },
      methods:{}
    }
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {
      message:"nihao"
    },
    methods: {},
    components: {
      cpn
    }
   });
  </script>
</body>

</html>
```

* 多个可能的属性：`propB:[String,Number]`

```html
//自定义属性 
function Person(name,age){
this.name=name
this.age=age
}
Vue.component('cpn'{
props:{
	author:Person
}
})
```

#### 驼峰问题

* 大小写之间用‘-’连接，大写字母改小写

#### 子传父

* 使用自定义事件

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <cpn @btn-click="cpnClick"></cpn>
  </div>
  <template id="cpn">
    <div>
      <button v-for="item in title" @click="btnClick(item)">
        {{item.name}}
      </button>
    </div>
  </template>

  <script>
    const cpn = {
      template:'#cpn',
      data(){
        return{
          title:[
            {id:"aaa",name:"小米"},
            {id:"bbb",name:"华为"},
            {id:"ccc",name:"三星"},
          ]
        }
      },
      methods:{
        btnClick(item){
          this.$emit("btn-click",item)
        }
      }
    }
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {
      cpnClick(item){
        console.log(item)
      }
    },
    components: {
      cpn
    }
   });
  </script>
</body>

</html>
```

### 组件之间访问

#### 父访问子

* 有两种访问方式：$childern和$refs访问方式
* $childern：一般用的少，需要指定下标；
* $refs：用的比较多，一般在子组件上添加一个id，用id进行访问；

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <cpn></cpn>
    <cpn></cpn>
    <cpn></cpn>
    <cpn ref="aaa"></cpn>
    <button @click="btnClick">按钮</button>
  </div>
  <template id="cpn">
    <h1>你好，组件</h1>
  </template>

  <script>
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {
      btnClick(){
        // console.log(this.$children[0])
        // this.$children[0].showMessage()
        // console.log(this.$children[0].name)
        this.$refs.aaa
        console.log(this.$refs.aaa.name)
      }
    },
    components: {
      cpn:{
        template:'#cpn',
        data () {
          return {
            name:"你好，我是子组件"
          }
        },
        methods:{
          showMessage(){
            console.log(this.name)
          }
        }
      }
    }
   });
  </script>
</body>

</html>
```

#### 子访问父

* 一般使用的$parent方法访问组件，但是也可以使用$root访问根组件的内容。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <cpn></cpn>
  </div>
  <template id="cpn">
    <div>
    <h1>你好，我是cpn</h1>
    <ccpn></ccpn>
  </div>
  </template>
  <template id="ccpn">
    <div>
      <h2>你好，我是ccpn</h2>
      <button @click="btnClick">按钮</button>
    </div>
  </template>

  <script>
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {
      title:"王五"
    },
    methods: {
    },
    components: {
      cpn:{
        template:'#cpn',
        data () {
          return {
          name:"张三"  
          }
        },
        components: {
          ccpn:{
            template:'#ccpn',
            methods:{
              btnClick(){
                console.log(this.$parent.name);
                console.log(this.$parent);
                console.log(this.$root.title);
              }
            }
          }
        }
      }
    }
   });
  </script>
</body>
</html>
```

### 插槽使用（slot）

#### 基本使用

* 插槽相当于一个多功能的接口，可以根据自己的想法进行自定义实现内容。
* 基本使用：`<slot></slot>`
* 插槽可以有默认值，在没有自定义时显示默认值，如果自定义了则显示自定义的内容。
* 如果自定义的内容为多个值时，同时放入插槽中替换时，一起作为替换元素进行替换

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app"> 
    <cpn></cpn>
    <cpn><i>hhaha</i></cpn>
    <cpn><span>nihao </span></cpn>
    <cpn></cpn>
    <cpn>
      <div>
        <h3>组件</h3> 
      </div>
      <h1>开发</h1>
      <div>我是div</div>
    </cpn>
    <cpn></cpn>
  </div>
  

  <template id="cpn">  
    <div>
      <h1>哈哈</h1>
      <h2>你好</h2>
      <slot><button>按钮</button></slot>
    </div>
  </template>

  <script>
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {},
    components: {
      cpn:{
        template:'#cpn'
      }
    }
   });
  </script>
</body>

</html>
```

#### 具名插槽

* 给一个插槽起一个名字，在有多个插槽的时候，可以根据插槽的名字进行修改插槽中的内容。
* 修改内容的时候不能使用`name`属性进行判断是那个插槽的，而是使用`slot='插槽的名字'`来进行确定。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app">
  <cpn>
    <button slot="left"><</button>
    <span slot="center">标题</span>
  </cpn>  
  </div>
  <template id="cpn">
    <div>
      <slot name="left"><span>111</span></slot>
      <slot name="center"><span>222</span></slot>
     <slot name="right"><span>333</span></slot>
    </div>
  </template>
  <script>
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {},
    components: {
      cpn:{
        template:'#cpn'
      }
    }
   });
  </script>
</body>

</html>
```

#### 编译的作用域

* 使用一些判断的东西，或者获取一些值都是看这个组件的在什么范围内，如果是vue范围内就使用vue组件内的数据如果实在子组件内，则使用子组件内的数据。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
  <div id ="app">
    <cpn v-show="!isShow"></cpn>
  </div>
  <template id="cpn">
    <div>
      <h1>nihao</h1>
      <p>hahah</p>
    </div>
  </template>
  <script src="../js/vue.js"></script>
  <script>
   const vm = new Vue({
    el: '#app',
    data: {
      message:'',
      isShow:true
    },
    methods: {
    },
     components:{
      cpn:{
        template:'#cpn',
        data(){
          return{
            isShow: false
          }
        }
      }
     }
   });
  </script>
</body>
</html>
```

#### 作用域插槽

* 父组件替换插槽中的标签，但是内容由子组件提供

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="../js/vue.js"></script>
</head>

<body>
  <div id ="app">
  <cpn></cpn>  
  <cpn>
    <template slot-scope="slot">
      <span>{{slot.data.join(' - ')}}</span>
    </template>
  </cpn> 
  <cpn>
    <template slot-scope="slot">
      <span>{{slot.data.join(' @ ')}}</span>
    </template>
  </cpn>   
  </div>
  <template id="cpn">
    <div>
      <slot :data="temp">
        <ul>
          <li v-for="item in temp">{{item}}</li>
        </ul>
      </slot>
    </div>
  </template>

  <script>
   //创建Vue实例,得到 ViewModel
   var vm = new Vue({
    el: '#app',
    data: {},
    methods: {},
    components:{
      cpn:{
        template:'#cpn',
        data () {
          return {
            temp:['java','c','c++','python','javascript','go']
          }
        }
      }
    }
   });
  </script>
</body>
</html>
```

## Vue-cli(脚手架)

### 创建一个项目

* 命令：vue create 项目名称

* 启动一个项目：npm run serve

* ```javascript
  //项目的构建信息
  Vue.config.productionTip = false
  ```

* `$mount('#app')`相当于el挂载。

### 修改配置

* 使用vue-ui命令可以在浏览器打开图形化界面，查看项目的依赖等其他东西。
* 在node_modules中的@vue文件中的cli-server文件中修改
* 需要在项目的根目录中添加一个vue.config.js文件进行配置，文件名不能改

### 箭头函数

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<script>
  //无参数和无返回值的箭头函数
  const ccc = ()=>{
  
  }
  //参数问题
  //1.1放入两个参数
  const aaa=(sum1,sum2)=>{
    return sum1+sum2
  }
  //1.2放入一个参数(小括号可以省略)
  const bbb= sum=>{
    return sum*sum
  }
  //2.
  //2.1有多行代码
  const x = () => {
    console.log('hello');
    console.log('nihao');
  }
  //2.2只有一行代码
  const x1 = (sun1,sun2) =>sun1*sun2
  console.log(x1(3, 7));
</script>

</body>
</html>
```

## vue-router

### 路由的基本配置

* 在src下添加一个router文件夹，在文件夹中添加一个index.js文件

  ```js
  //配置路由相关的信息
  import VueRouter from "vue-router";
  
  //1.通过vue.use(插件)，安装插件
  Vue.use(VueRouter)
  
  //2.创建路由对象
  const router = new VueRouter({
    //配置路由和组建之间的映射关系
    routes:[]
  })
  
  //将router对象传入到vue中
  export default router
  ```

* 在main导入router

  ```js
  import Vue from 'vue'
  import App from './App.vue'
  //导入router
  import router from "./router";
  
  Vue.config.productionTip = false
  
  new Vue({
    //挂载路由
    router,
    render: h => h(App)
  
  }).$mount('#app')
  
  ```

### 路由的使用

* 创建需要进行的路由项目

![image-20211213215828822](/Users/gyf/Library/Application Support/typora-user-images/image-20211213215828822.png)

* 将新建的项目导入到router文件夹下的index.js文件中，并在进行使用，每一个项目相当于一个对象，需要使用path设置浏览器中需要访问的路径，使用component进行项目的挂载。

  ```js
  //配置路由相关的信息
  import Vue from 'vue'
  import VueRouter from "vue-router";
  
  import Home from "../components/Home";
  import About from "../components/About";
  
  //1.通过vue.use(插件)，安装插件
  Vue.use(VueRouter)
  
  //2.创建路由对象
  const router = new VueRouter({
    //配置路由和组建之间的映射关系
    routes:[
      //默认值
      {
        path:'',
        //重定向
        redirect:'/home'
      },
      {
        path:'/home',
        component:Home
      },
      {
        path:'/about',
        component:About
      }
    ],
    mode:'history'
  })
  
  //将router对象传入到vue中
  export default router
  ```

* 在App.vue进行使用

  * 使用router-link访问路径;

    * to属性：跳转页面。
    * tag：可以指定router-link渲染成什么组件。
    * replace：当有这个属性时，不可以使用左上角的返回标志
    * active-class：设置link的样式，也可以在路由中添加linkactiveclass来控制样式。

  * 使用router-view进是渲染内容,router-view只是一个占位符

  * ```js
    <template>
      <div id="app">
        <router-link to="/home">首页</router-link>
        <router-link to="/about">关于</router-link>
        <router-view></router-view>
      </div>
    </template>
    
    <script>
    export default {
      name: 'App',
    
    }
    </script>
    
    <style>
    
    </style>
    ```

  * 修改浏览器输入框去掉#

    在router的index.js中添加`mode:'history'`;

### 使用代码跳转

```vue
<template>
  <div id="app">
<!--    <router-link to="/home" tag="button" replace  >首页</router-link>-->
<!--    <router-link to="/about" tag="button" replace >关于</router-link>-->
    <button @click="homeClick">首页</button>
    <button @click="aboutClick">关于</button>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    homeClick() {
      this.$router.push('/home')
      console.log('homeClick')
    },
    aboutClick(){
      this.$router.push('/about')
      console.log('aboutClick');
    }
  }
}
</script>

<style>
</style>

```

### 动态路由

* 浏览器输入览可以进行动态的修改

* 想要实现动态路由必须使to属性绑定v-bind

* App.vue

  ```vue
  <template>
    <div id="app">
      <router-link to="/home" tag="button" replace  >首页</router-link>
      <router-link to="/about" tag="button" replace >关于</router-link>
      <router-link :to="'/user/'+user" tag="button" replace>我的</router-link>
  <!--    <button @click="homeClick">首页</button>-->
  <!--    <button @click="aboutClick">关于</button>-->
      <router-view></router-view>
    </div>
  </template>
  
  <script>
  export default {
    name: 'App',
    data(){
      return{
        user:'lisi1'
      }
    },
    methods: {
      homeClick() {
        this.$router.push('/home')
        console.log('homeClick')
      },
      aboutClick(){
        this.$router.push('/about')
        console.log('aboutClick');
      }
    }
  }
  </script>
  
  <style>
  </style>
  
  ```

* User.vue

  ```vue
  <template>
  <div>
    <h2>我是用户界面</h2>
    <h3>大家好，我是：{{userId}}</h3>
    <h1>{{$route.params.userid}}</h1>
  </div>
  </template>
  
  <script>
  export default {
    name: "User",
    computed:{
      userId(){
        return this.$route.params.userid
      }
    }
  }
  </script>
  
  <style scoped>
  
  </style>
  ```

### 懒加载

* 将不同的路由划分到不同的js文件中，在调用的时候在进行加载
* 懒加载的三种方式，现在常用第三种

![image-20211214210311550](/Users/gyf/Library/Application Support/typora-user-images/image-20211214210311550.png)

### 路由的嵌套

* 在一个路由中嵌套其他路由

![image-20211214212848167](/Users/gyf/Library/Application Support/typora-user-images/image-20211214212848167.png)

* 使用children来进行修饰，子路由都在childern中，path路径不需要写斜线

* 展示不能在app.vue中展示，而是在父路由的vue中进行展示,router-link时必须写全路径，否则vue不会找到的

  ```vue
  <template>
  <div>
    <h2>我是首页</h2>
    <h2>哈哈哈哈</h2>
    <router-link to="/home/news" tag="button">新闻</router-link>
    <router-link to="/home/message" tag="button">信息</router-link>
    <router-view></router-view>
  </div>
  </template>
  
  <script>
  export default {
    name: "Home"
  }
  </script>
  
  <style scoped>
  
  </style>
  ```

### 参数的传递

* 方法一：使用动态路由进行传递

* 方法二：使用query(对象)进行传递，正常挂载路由，在调用路由使用v-bind绑定to属性进行传参。使用时调用$route.query.key值

  ```vue
      <router-link :to="{path:'/profile',query:{name:'张三'}}" tag="button" >档案</router-link>
  
  <!--调用-->
    <h2>{{$route.query.name}}</h2>
  ```


### 全局导航守卫

* 可以在调用不同的首页标题展示不同的内容。

* 调用了router中的方法。

* 使用方法

  * 在路由的index.js文件中，在创建路由之后调用路由的方法beforeEach(),切记在方法内容中一定要调用next()方法，否则不会往下执行。

  * 在每一个需要展示title的路由中添加属性meta(元数据)，在meta中可以添加title熟悉。

  * 在调用时由于用到了路由嵌套，在展示的时候需要调用meta的上一级matched，由于一级标题在第一位，所以一般直接调用matched[0]即可。

  * 其中设计到三个参数：

    * to：即将要进入的目标的路由对象
    * from：当前导航即将离开啶对象
    * next：调用该方法后，才能进入下一个钩子

    ```js
    //配置路由相关的信息
    import Vue from 'vue'
    import VueRouter from "vue-router";
    
    // import Home from "../components/Home";
    // import About from "../components/About";
    // import User from "../components/User";
    
    //1.通过vue.use(插件)，安装插件
    Vue.use(VueRouter)
    const Home = ()=>import('../components/Home')
    const HomeNews = ()=>import('../components/HomeNews')
    const HomeMessage = ()=>import('../components/HomeMessage')
    const About = ()=>import('../components/About')
    const User = ()=>import('../components/User')
    const Profile = ()=>import('../components/profile')
    
    
    const routes=[
        {
          path:'',
          //重定向
          redirect:'/home'
        },
        {
          path:'/home',
          component:Home,
          meta:{
            title:'首页'
          },
          children:[
            {
              path: '',
              redirect:'news'
            },
            {
              path:'news',
              component:HomeNews
            },
            {
              path: 'message',
              component:HomeMessage
            }
          ]
        },
        {
          path:'/about',
          component:About,
          meta:{
            title:'关于'
          }
        },
        {
          path: '/user/:userid',
          component: User,
          meta:{
            title:'我的'
          },
        },
        {
          path: '/profile',
          component:Profile,
          meta:{
            title:'档案'
          },
        }
      ]
    //2.创建路由对象
    const router = new VueRouter({
      //配置路由和组建之间的映射关系
      routes,
      mode:'history',
      // linkActiveClass:'active'
    
    })
    router.beforeEach((to, from, next)=>{
      document.title=to.matched[0].meta.title
      console.log(to);
      next()
    })
    
    //将router对象传入到vue中
    export default router
    ```

* 现在使用的都是全局守卫，还有路由独享的守卫可以在每个路由中进行判断。注意<span style='color:red'>内容是beforeEnter</span>

  ```js
  {
        path:'/about',
        component:About,
        meta:{
          title:'关于'
        },
        beforeEnter:(to,from,next)=>{
          console.log('我是关于');
          next()
        }
      },
  ```


### keep-alive

* 使用keep-alive包裹着router-view可以使路由缓存，不会频繁的创建和销毁，只会变成活跃和不活跃的状态，其中包括两个属性
  * include-只有匹配的组件才会被缓存
  * exclude：任何匹配的组件都不会被缓存
