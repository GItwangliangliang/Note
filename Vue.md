

# Vue2核心

1.想让Vue工作，就必须创建一个Vue实例，并且传入一个配置对象

2.root容器里的代码依然符合HTML规范，只不过混入一些特殊的Vue语法

3.roor容器里的代码被称为【Vue模板】

4.真实开发Vue实例只有一个，会配合组件一起使用

5.{{XXX}}种要写js表达式，XXX可以自动读取到data中的所有属性

6.Vue实例和容器是一一对应的

7.一旦data中的数据发生变化，那么页面中用到该数据的地方也会自动更新

注意 区分JS表达式 和 Js代码

```HTML
 <!-- 准备一个容器-->
     <div id="root">
        <h1>Hello {{name}} {{address}}</h1>
     </div>

    <script type="text/javaScript">
        Vue.config.productionTip = false
        Vue.config.devtools = true
         //创建Vue示例
        const x = new Vue({
            el:'#root',  //指定当前Vue示例为哪个容器服务，值通常为css选择器字符串
            // el:document.geElementById('root')
            data:{ // data用于存储数据，数据供el所指定的容器所使用，值暂时先写成一个对象
                name: 'wang liang liang',
                address: "NUIST"
            }
        })

    </script>
```

## 数据绑定

```HTML
<div id="root">
        <!-- 单向数据绑定：<input type="text" v-bind:value="name"><br/>
        双向数据绑定：<input type="text" v-model:value="name"> -->
        
        单向数据绑定：<input type="text" :value="name"><br/>
        双向数据绑定：<input type="text" v-model="name">
</div>
```

## 模板语法

1.插值语法：

用于解析标签体内容

{{XXX}}，XXX是js表达式，

2.指令语法

用于解析标签（包括标签属性、标签体内容，绑定事件）

v-bind:herf="" 

:herf=""

```HTML
<div id="root">
        <h1>插值语法</h1>
        <h3>你好,{{name}}</h3>
        <hr/>
        <h1>指令语法</h1>
        <a v-bind:href="url">点我跳转Bilibli</a>
        <br/>
        <a :href="url">点我跳转百度</a>
</div>
<script type="text/javaScript" >
        Vue.config.productionTip = false
        new Vue({
            el:'#root',
            data:{
                name:'wang',
                url:'https://www.bilibili.com/'
            }
        })
</script>
```

### el与data

```HTML
<script type="text/javaScript">
        Vue.config.productionTip = false
        <!--el写法-->
        // const v= new Vue({
        //     // el:'#root', 第一种写法
        //     data:{
        //         name:'wang'
        //     }
        // })
        // console.log(v);
        // v.$mount('#root') //第二种语法

        <!--data写法-->
        new Vue({
            el:'#root',
            // data:{  第一种写法：对象式
            //     name:'wang'
            // }
            
            // 函数式
            data:function(){
                console.log(this)    
                return {
                    name:'test_wang',
                }
        }
        })
</script>
```

## MVVM

![image-20240715152501800](E:\Typora_Image\image-20240715152501800.png)

## 数据代理

```javascript
 <script type="text/javaScript">
    Vue.config.productionTip = false
    let num = 18
    let person = {
        name:'wang',
        sex:'男',
    }

    Object.defineProperty(person, 'age',{
        value:18, 
        enumerable:true,  //控制属性是否可以枚举。默认false
        writable:true,  //控制属性是否可以被修改。默认false
        configurable:true //控制属性是否可以被删除。默认false
        // 当有人读取person的age属性时，get函数就会被调用，返回值就是age的值
        get:function() {
            return num
        }
        // 当有人修改person的age属性时，set函数就会被调用，并且会收到修改的具体值
        set:function(value) {
            num = value
        }
    })
    console.log(person)
</script>
```

修改 `obj2.x` 时，确实会触发 setter 方法，从而修改 `obj1.x` 的值。然而，`obj2.x` 并不会真正存储这个值，而是通过 getter 和 setter 方法动态地与 `obj1.x` 进行关联。因此，表面上看起来像是 `obj2.x` 被修改了，但实际上是 `obj1.x` 被修改了

```javascript
<script type="text/javaScript">
        Vue.config.productionTip = false
        let obj1 = {x:100}
        let obj2 = {y:200}

        Object.defineProperty(obj2, 'x', {
            get(){
                console.log("$get")
                return obj1.x
            },

            set(value){
                console.log("$set")
                obj1.x=value
            }
        })
</script>
```

>  **vm中的数据代理**：通过vm对象来代理data对象中属性的操作
>
>  **好处**：更加方便的操作data中的数据
>
> **基本原理**：通过Object.defineProperty()把data对象中所有属性添加到vm上。为每一个添加到vm上的属性，都指定一个getter/setter。在getter/setter内部去操作data中对应的属性。

```css
<div id="root">
    <h2>name:{{name}}</h2>
    <h2>add:{{add}}</h2>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:"#root",
        data:function () {
            return {
                name:'wang',
                add:'NUIST'
            }
        }
    })
</script>
```

![1720489800154](E:\Typora_Image\1720489800154.jpg)

## 事件处理

1. 使用v-on:XXX或者@xxx绑定事件。其中XXX是事件名

2. 事件的回调需要配置在methods对象中，最终会在vm上
3. methods中配置的函数不要用箭头函数
4. methods中配置的函数都是被Vue所管理的函数，this指向的是vm或者组件示例对象
5. @click="demo"和@click="demo($event)"效果一致，但后者可以传参

```html
 <div id="root">
    <h2>这里是{{add}}</h2>
    <!-- <button v-on:click="showInfo">点我提示信息</button> -->
    <button @click="showInfo1">点我提示信息1(不传参数)</button>
    <button @click="showInfo2(66, $event)">点我提示信息2(传参数)</button>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:"#root",
        data:function () {
            return {
                name:'wang',
                add:'NUIST'
            }
        },
        methods:{
            showInfo1(event){
                // console.log(this) 这里的this是vm
                alert('你好1')
            },
            showInfo2(num, event){
                // console.log(this) 这里的this是vm
                console.log(num, event)
                alert(`你好${num}`)
            }
        }
    })
</script>
```

### 事件修饰符

1. prevent:阻止默认事件
2. stop:阻止事件冒泡
3. once:事件只触发一次
4. capture:使用事件的捕获模式
5. self:只有envent.target是当前操作的元素时才触发事件
6. passive:事件的默认行为立即执行，无需等待事件回调执行完成

```html
<div id="root">
    <h2>这里是{{add}}</h2>
    <!-- <button v-on:click="showInfo">点我提示信息</button> -->
     <a href="https://www.bilibili.com/" @click.prevent="showInfo">阻止默认事件</a>

     <!--阻止事件冒泡-->
     <div class="demo1" @click="bubbling">
        <button  @click.stop="showInfo">阻止事件冒泡</button>
    </div>
    
    <!--修饰符可以连续写-->
    <a href="https://wwww.baidu.com" @click.stop.prevent="showInfo">点我提示</a>
    
    <!--事件只触发一次-->
    <button @click.once="showInfo">事件只触发一次</button>

    <!--事件的捕获模式-->
    <p>事件的捕获模式</p>
    <div class="box1"  @click.capture="showMsg(1)">
        div1
        <div class="box2" @click="showMsg(2)">
            div2
        </div>
    </div>

    <!--只有envent.target是当前操作的元素时才触发事件-->
    <div class="demo1" @click.self="bubbling">
        <button @click="showInfo">点我提示信息</button>
    </div>

    <!--事件的默认行为立即执行，无需等待事件回调执行完成-->
    <ul @wheel.passive="demo" class="list">
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
    </ul>
</div>


<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:"#root",
        data:function () {
            return {
                name:'wang',
                add:'NUIST'
            }
        },
        methods:{
            showInfo(event){
                // console.log(this) 这里的this是vm
                alert('你好')
            },
            bubbling(event){
                // console.log(this) 这里的this是vm
                alert('冒泡')
            },
            showMsg(msg){
                console.log(msg)
            },
            demo(){
                for (let index = 0; index < 100000; index++) {
                    console.log(index)   
                }
                console.log("结束")
            }
        }
    })
</script>
```

### 键盘事件

1. Vue中常见的按键别名

   ​	回车 enter

   ​	删除 delete

   ​	退出  esc

   ​	空格  space

   ​	换行 tab （配合keydown使用）

   ​	上 up 下 down 左 left 右 right

2. ctrl alt shift meta 

   - 配合keyup使用：按下修饰键的同时，再按下其它建，随后释放其它键。事件才触发
   - 配合keydown使用正常触发事件

```html
<div id="root">
        <h2>欢迎来到{{name}}</h2>
        <input type="text" placeholder="按下回车提示输入" @keyup.enter="showInfo">
</div>

<script type="text/javaScript">
        Vue.config.productionTip = false
    
        const vm = new Vue({
            el: '#root',
            data:function() {
                return {
                    name:'NUIST'
                }
            },
            methods:{
                showInfo(e){
                    console.log(e.target.value)
                }
            },
        })
</script>
```

## 计算属性与监视

### 计算属性

插值语法实现

```html
<div id="root">
        <h2>欢迎来到{{name}}</h2>
        <input type="text" placeholder="按下回车提示输入" @keyup.enter="showInfo">
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false

    const vm = new Vue({
        el: '#root',
        data:function() {
            return {
                name:'NUIST'
            }
        },
        methods:{
            showInfo(e){
                console.log(e.target.value)
            }
        },
    })
</script>
```

用method实现

```html
<div id="root">
        姓：<input type="text" v-model="name_1"><br/><br/>
        名：<input type="text" v-model="name_2"><br/><br/>
        姓名：<span>{{fullname()}}</span>
    </div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:"#root",
        data: function() {
            return {
                name_1:'wang',
                name_2:'liang'
            }
        },
        methods:{
            fullname(){
                return this.name_1+'-'+this.name_2
            }
        }
    })
</script>
```

计算属性

1. 要用的属性不存在，通过计算属性计算得来

2. 底层借助了Object.defineproperty方法提供的getter和setter

3. get函数什么时候执行？

   初次读取时会执行一次

   当依赖的数据发生变化时再次被调用

4. 与menthods实现相比，内部有缓存机制，效率更高，调试方便

5. 计算属性最终会出现再vm上，直接读取使用即可。如果计算属性要被修改，那必须写set函数去响应修改，并且set中要引起计算时依赖数据发生的变化

```html
<div id="root">
        姓：<input type="text" v-model="name_1"><br/><br/>
        名：<input type="text" v-model="name_2"><br/><br/>
        姓名：<span>{{fullname}}</span>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:"#root",
        data: function() {
            return {
                name_1:'wang',
                name_2:'liang'
            }
        },
        computed:{
            //完整
            // fullname:{
            //     get(){
            //         console.log('get')
            //         return this.name_1 + '-' + this.name_2
            //     },

            //     set(value){
            //         console.log('set')
            //         const arr = value.split('-')
            //         this.name_1 = arr[0]
            //         this.name_2 = arr[1]
            //     }
            // }

            //简写
            fullname(){
                console.log('get')
                return this.name_1 + '-' + this.name_2
            }
        }
    })
</script>
```

### 监视属性

监视的属性存在才能监视

```html
<div id="root">
    <h2>今天天气{{info}}</h2>
    <button @click="changeWeathrer">切换天气</button>
    <hr/>
    <h3>a:{{number.a}}</h3>
    <button @click="number.a++">点击a+1</button>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data:{
            isHot:true
        },
        computed:{
            info(){
                return this.isHot?'炎热':'凉爽'
            }
        },
        methods: {
            changeWeathrer(){
                this.isHot = !this.isHot
            }
        },
        // watch:{
        //     isHot:{
        //         immediate:true,
        //         handler(new_value, old_value){
        //             console.log('修改')
        //             console.log(new_value, old_value)
        //         }
        //     }
        // }
        })
    vm.$watch('isHot', {
        immediate:true,
        handler(new_value, old_value){
            console.log('修改')
            console.log(new_value, old_value)

        }
    //简写
    vm.$watch('isHot',function(newvalue, oldvalue){
        console.log('isHot被修改',newvalue, oldvalue)
    })
    })
</script>
```

深度监视

1. vue中的watch默认不监视对象内部值得改变（一层）
2. 配置deep:true 可以检测对象内部值得改变（多层）
3. vue自身可以检测对象内部值的改变，但vue提供的watch默认不认识
4. 使用watch时根据数据的具体结构，决定是否采用深度监视

```html
<div id="root">
    <h2>今天天气{{info}}</h2>
    <button @click="changeWeathrer">切换天气</button>
    <hr/>
    <h3>a:{{number.a}}</h3>
    <button @click="number.a++">点击a+1</button>
    <hr/>
    <h3>b:{{number.b}}</h3>
    <button @click="number.b++">点击b+1</button>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data:{
            isHot:true,
            number:{
                a:1,
                b:1
            }
        },
        computed:{
            info(){
                return this.isHot?'炎热':'凉爽'
            }
        },
        methods: {
            changeWeathrer(){
                this.isHot = !this.isHot
            }
        },
        watch:{
            isHot:{
                immediate:true,
                handler(new_value, old_value){
                    console.log('修改')
                    console.log(new_value, old_value)
                }
            },
            //简写
            isHot(new_value, old_value){
                console.log('isHot被修改', new_value, old_value)
            }
            
            //监视多级结构
            // "number.a":{
            //     immediate:true,
            //     handler(){
            //         console.log('a被修改')
            //     }
            // }
            //监视多级结构中所有属性的变化
            number:{
                deep:true,
                handler(){
                    console.log('number被修改')
                }
            }
        }
        })
</script>
```

<font color=red>computed和watch之间的区别</font>

1. **computed能完成的功能，watch都可以完成**
2. **watch能完成的功能，conmputed不一定完成，例如：watch可以进行异步操作**

<font color=red>两点小原则</font>

1. 所有被vue管理的函数，最好写成普通函数，这样this才指向vm或者组件示例对象
2. 所有不被vue所管理的函数（定时器的回调函数、ajax的回调函数，promise的回调函数），最好写成箭头函数。这样this的指向才是vm或者组件实例对象

## 绑定class样式

```html
<div id="root">
    <!--绑定class样式--字符串写法，适用于样式的类名不确定，需要动态指定-->
    <div class="basic" :class="s" @click="changeState">{{name}}</div>
    <br/><br/>
    <!--绑定class样式--数组写法，适用于要绑定的样式个数名字都不确定-->
    <div class="basic" :class="arr">{{name}}</div>
    <br/><br/>
    <!--绑定class样式--对象写法，适用于要绑定的样式个数确定，名字确定，动态决定用不用-->
    <div class="basic" :class="classobj">{{name}}</div>
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data:function() {
            return {
                name:"NUIST",
                s:"state_1",
                arr:['state_2', 'state_3', 'state_1'],
                classobj:{
                    state_1:false,
                    state_2:false
                }

            }
        },
        methods: {
            changeState(){
                const state = ['state_1', 'state_2', 'state_3']
                this.s = state[Math.floor(Math.random()*3)]
            }
        },
    })
</script>
```

## 条件渲染

1. v-if适用于切换频率比较低的场景，不展示的DOM元素直接被移除

   <font color=red>注意v-if可以和v-else-if、v-else一起使用，但要求结构不能被打断</font>

2. v-show适用于切换频率较高的场景，不展示的DOM元素未被移除，仅仅是使用样式隐藏掉

```html
<div id="root">
    <h2>当前的n值是{{n}}</h2>
    <button @click="n++">点我加一</button>
    <!-- <div v-show="n===1">Angular</div>
    <div v-show="n===2">React</div>
    <div v-show="n===3">Vue</div> -->

    <div v-if="n===1">Angular</div>
    <div v-else-if="n===2">React</div>
    <div v-else-if="n===3">Vue</div>
    <div v-else>test</div>

    <template v-if="n===1">
        <h2>南京</h2>
        <h2>北京</h2>
    </template>

    <!--使用v-show做条件渲染-->
    <!-- <h2 v-show="true">欢迎来到{{name}}</h2> -->

    <!--使用v-if做条件渲染-->
    <!-- <h2 v-if="false">欢迎来到{{name}}</h2> -->
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:"#root",
        data:function() {
            return {
                name:"NUIST",
                n: 0
            }
        }

    })
</script>
```

## 列表渲染

v-for指令用于展示列表数据

可遍历：数组、对象、字符串、指定次数

```html
<div id="root">
    <!--遍历数组-->
    <ul>
        <li v-for="(p, key) in person_list" :key="key">{{p.name}}-{{p.age}}-{{key}}</li>
        <!-- <li v-for="(p, key) in person_list" :key="p.ID">{{p.name}}-{{p.age}}</li> -->
    </ul>

    <!--遍历对象-->
    <ul>
        <li v-for="(k, v) in car" :key="k">{{k}}-{{v}}</li>
        <!-- <li v-for="(p, key) in person_list" :key="p.ID">{{p.name}}-{{p.age}}</li> -->
    </ul>

    <!--遍历字符串-->
    <ul>
        <li v-for="(char, index) in str" :key="index">{{char}}-{{index}}</li>
        <!-- <li v-for="(p, key) in person_list" :key="p.ID">{{p.name}}-{{p.age}}</li> -->
    </ul>

    <!--遍历指定次数-->
    <ul>
        <li v-for="(num, index) in 10" :key="index">{{num}}-{{index}}</li>
        <!-- <li v-for="(p, key) in person_list" :key="p.ID">{{p.name}}-{{p.age}}</li> -->
    </ul>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    new Vue({
        el:"#root",
        data:function() {
            return {
                person_list:[
                    {ID:'001',name:'wang',age:18},
                    {ID:'002',name:'liang',age:20},
                    {ID:'003',name:'liang',age:23},
                ],
                car:{
                    name:'audi',
                    price:'70w',
                    color:'red'

                },
                str:'vue'
            }
        }
    })
</script>
```

### key作用与原理

#### <font color=red size=5>**react、vue中的key有什么作用**？</font>

1. 虚拟DOM中的key作用

   - key是虚拟DOM对象的标识，当状态中的数据发生变化时。Vue会根据【新数据】生成【新的虚拟DOM】

     随后VUe进行【新虚拟DOM】与【旧虚拟DOM】的差异比较

2. 对比规则

   - 旧虚拟DOM中找到与新虚拟DOM相同的key

     ​	若虚拟DOM中的内容没变，直接使用之前的真实DOM

     ​	若虚拟DOM中的内容变了，则生成新的真实的DOM，随后替换掉页面中之前的真实DOM

   - 旧虚拟DOM中未找到与新虚拟DOM相同的key

     ​	创建新的真实的DOM，随后渲染到页面

3. 用index作为key会引发的问题

   - 若对数据进行逆序添加、逆序删除等破坏操作：会产生没有必要的真实DOM更新==>界面效果没问题，但效率低下

   - 如果结构中包含输入类的DOM，会产生错误的DOM更新==>界面有问题

4. 开发中如何选择key

   - 最好使用每条数据的唯一标识
   - 如果不存在对数据的逆序添加、逆序删除等破坏性操作，仅用于渲染列表用于展示，使用index作为key是没有问题的

![image-20240706102630917](E:\Typora_Image\image-20240706102630917.png)

```html
<div id="root">
    <!--遍历数组-->
    <button @click.once="add">添加一个人</button>
    <ul>
        <li v-for="(p, key) in person_list" :key="key">{{p.name}}-{{p.age}}-{{key}}
            <input type="text">
        </li>
        <!-- <li v-for="(p, key) in person_list" :key="p.ID">{{p.name}}-{{p.age}}</li> -->
    </ul>

</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    new Vue({
        el:"#root",
        data:function() {
            return {
                person_list:[
                    {ID:'001',name:'wang',age:18},
                    {ID:'002',name:'liang',age:20},
                    {ID:'003',name:'liang',age:23},
                ],
            }
        },
        methods: {
            add(){
                const p = {ID:'004',name:'nuist',age:64}
                this.person_list.unshift(p)
            }
        },

    })
</script>
```

![1720233148649](E:\Typora_Image\1720233148649.jpg)

### 列表过滤

```html
<div id="root">
    <h2>人员列表</h2>
    <input type="text" placeholder="输入名字" v-model="keyword">
    <ul>
        <li v-for="(p, index) of filPerson" :keys="index">
            {{p.name}}-{{p.age}}-{{p.sex}}
        </li>
    </ul>
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false
    // const vm = new Vue({
    //     el:"#root",
    //     data: function() {
    //         return {
    //             keyword:'',
    //             person_list:[
    //                 {ID:'001',name:'马冬梅',age:18, sex:'女'},
    //                 {ID:'002',name:'周冬雨',age:20, sex:'女'},
    //                 {ID:'003',name:'周杰伦',age:23, sex:'男'},
    //                 {ID:'004',name:'温兆伦',age:23, sex:'男'},
    //             ],
    //             filPerson:[]
    //         }
    //     },
    //     watch:{
    //         keyword:{
    //             immediate:true,
    //             handler(val){
    //                 this.filPerson = this.person_list.filter((p)=>{
    //                 return p.name.indexOf(val) !==-1
    //             })
    //             }
    //         }
    //     }
    // })

    // 计算属性实现
    const vm = new Vue({
        el:'#root',
        data:function() {
            return {
                keyword:'',
                person_list:[
                    {ID:'001',name:'马冬梅',age:18, sex:'女'},
                    {ID:'002',name:'周冬雨',age:20, sex:'女'},
                    {ID:'003',name:'周杰伦',age:23, sex:'男'},
                    {ID:'004',name:'温兆伦',age:23, sex:'男'},
                ],

            }
        },
        computed:{
            filPerson(){
                return this.filPerson = this.person_list.filter((p)=>{
                    return p.name.indexOf(this.keyword) !==-1
                })
            }
        }
    })
</script>
```

### 列表排序

```html
<div id="root">
    <h2>人员列表</h2>
    <input type="text" placeholder="输入名字" v-model="keyword">
    <button @click="sortkey=2">年龄升序</button>
    <button @click="sortkey=1">年龄降序</button>
    <button @click="sortkey=0">原顺序</button>
    <ul>
        <li v-for="(p, index) of filPerson" :keys="index">
            {{p.name}}-{{p.age}}-{{p.sex}}
        </li>
    </ul>
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false
    // 计算属性实现
    const vm = new Vue({
        el:'#root',
        data:function() {
            return {
                keyword:'',
                sortkey:0,
                person_list:[
                    {ID:'001',name:'马冬梅',age:18, sex:'女'},
                    {ID:'002',name:'周冬雨',age:20, sex:'女'},
                    {ID:'003',name:'周杰伦',age:99, sex:'男'},
                    {ID:'004',name:'温兆伦',age:23, sex:'男'},
                ],

            }
        },
        computed:{
            filPerson(){
                const arr = this.filPerson = this.person_list.filter((p)=>{
                    return p.name.indexOf(this.keyword) !==-1
                })
                if(this.sortkey){
                    arr.sort((a, b)=>{
                        return this.sortkey == 1 ? b.age-a.age : a.age-b.age
                    })
                }
                return arr
            }
        }
    })
</script>
```

### Vue数据监视

1. Vue会监视data中所有层次的数据

2. 如何监视对象中的数据

   - 通过setter实现监视，并且要在new vue时就要传入监视的数据

     - 对象中后添加的属性，Vue默认不做响应式处理

     - 如果给后添加的属性做响应式处理

       ​	Vue.set(target, propertyName/index, value)

       ​	Vue.$set(target, propertyName/index, value)

3. 如何监视数组中的数据

   - 通过包裹数组更新元素的方法实现，本质就是
     - 调用原生对应的方法对数组进行更新
     - 重新解析模板，进而进行页面更新

4. 在Vue中修改数组中的某个元素一定要用下面的方法

   - 使用这些API：push()、pop()、shift()、unshift()、splice()、reverse()
   - Vue.set()或者Vue.$set()

<font color=red size=5>Vue.set()和Vue.$set()不能给vm或vm的根数据添加属性</font>

```html
<div id="root">
    <h2>school:{{name}}</h2>
    <h2>school_add:{{add}}</h2>
    <hr/>
    <h1>information</h1>

    <button @click="student.age.rAge++">年龄加一</button> <br/><br/>
    <button @click="addsex">添加性别属性，默认值:男</button> <br/><br/>
    <button @click="student.sex='test'">修改性别</button> <br/><br/>
    <button @click="addfriend">在列表首位加一个朋友</button> <br/><br/>
    <button @click="updatefirendsfirst">年龄修改第一个朋友的名字为张三</button> <br/><br/>
    <button @click="addhobby">添加一个爱好</button> <br/><br/>
    <button @click="updatehobby">修改第一个爱好为drive</button> <br/><br/>

    <h2>student:{{student.name}}</h2>
    <h2 v-if="student.sex">sex:{{student.sex}}</h2>
    <h2>student_age:{{student.age.rAge}}  {{student.age.sAge}}</h2>

    <h2>hobby</h2>
    <ul>
        <li v-for="(h, index) in student.hobby" :key="index">
            {{h}}
        </li>

    </ul>

    <h2>firends</h2>
    <ul>
        <li v-for="(f, index) in student.firends" :key="index">
            {{f.name}}--{{f.age}}
        </li>

    </ul>

</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data: function() {
            return {
                name:'NUIST',
                add:'Nanjing',
                student:{
                    name:'wang',
                    age:{
                        rAge:26,
                        sAge:22,
                    },
                    hobby:['pingpang', 'run', 'game'],
                    firends:[
                        {name:'tao', age:24},
                        {name:'ning', age:23}
                    ]
                }
            }
        },
        methods: {
            addsex(){
                // Vue.set(this.student, 'sex', 'man')
                this.$set(this.student, 'sex', 'man')
            },
            addfriend(){
                this.student.firends.unshift({name:'jack', age:70})
            },
            updatefirendsfirst(){
                this.student.firends[0].name = '张三'
                this.student.firends[0].age = 30
            },
            addhobby(){
                this.student.hobby.push('study')
            },
            updatehobby(){
                // this.student.hobby.splice(0,1,'drive')
                this.$set(this.student.hobby, 0, 'drive')
            }
        },
    })
</script>

```

## 收集表单数据

若<input type='text'/>则v-model收集的是value的值，用户输入的就是value的值

若<input type='radio'/>则v-model收集的是value的值，并且要给标签设置value的值

若<input type='checkbox'/>

- 没有配置input的value属性，那么收集的就是checked（勾选or不勾选，是布尔值）
- 配置input的value属性
  - v-model的初始值是非数组，那么收集的就是checked（勾选or不勾选，是布尔值） 
  - v-model的初始值是数组，那么收集的就是value组成的数组、

v-model的三个修饰符：

- lazy 失去焦点再收集数据
- number 输入字符串转为有效数字
- trim 输入首尾空格过滤

```html
<div id="root">
    <form @submit="demo">
        <label for="ac">账号：</label>
        <input type="text" v-model.trim="userinfo.account" id="ac"><br/><br/>

        <label for="pa">密码：</label>
        <input type="text" v-model="userinfo.password" id="pa"><br/><br/>

        年龄：<input type="number" v-model.number="userinfo.age"><br/><br/>

        性别：
        男<input type="radio" name="sex" v-model="userinfo.sex" value="male">
        女<input type="radio" name="sex" v-model="userinfo.sex" value="female"><br/><br/>

        爱好：
        乒乓球:<input type="checkbox" v-model="userinfo.hobby" value="pingpang">
        跑步:<input type="checkbox" v-model="userinfo.hobby" value="run">
        游戏:<input type="checkbox" v-model="userinfo.hobby" value="game"><br/><br/>

        校区：
        <select v-model="userinfo.city">
            <option value="puk">浦口</option>
            <option value="jniuh">金牛湖</option>
        </select><br/><br/>

        其它信息：
        <textarea v-model.lazy="userinfo.other"></textarea><br/><br/>
        <input type="checkbox" v-model="userinfo.agree">阅读并接受<a href="https://www.baidu.com">《用户协议》</a><br/><br/>
        <button>提交</button>
    </form>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data:{
           userinfo:{
                account:'',
                password:'',
                sex:'female',
                hobby:[],
                city:'puk',
                other:'',
                agree:'',
                age:''
            }
        },
        methods: {
            demo(){
                alert('已提交')
                alert(JSON.stringify(this.userinfo))
            }
        },
    })
</script>
```

## 过滤器

对显示的数据进行格式化后再显示

语法：

1. 注册过滤器 Vue.filter(name,callback)或new Vue{filter:{}}
2. 使用过滤器 {{xxx | 过滤器名}}或v-bind:属性 = "xxx | 过滤器名"

备注：

1. 过滤器可以接收额外参数、多个过滤器可以串联
2. 并没有改变原来的数据，是产生新的对应得数据

```html
<div id='root'>
    <!--计算属性-->
    <h2>现在时间是{{fmttime}}</h2>
    <!--methods-->
    <h2>现在时间是{{getfulltime()}}</h2>
    <!--过滤器-->
    <h2>现在时间是{{time | timeFormater}}</h2>
    <h2>现在时间是{{time | timeFormater('YYYY-MM-DD')| mySlice}}</h2>
    <h3 :x="msg | mySlice">NUIST</h3>
</div>

<div id="root_1">
    <h2>{{msg | mySlice}}</h2>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    //全局过滤器
    Vue.filter('mySlice', function(value) {
        return value.slice(0,4)
    })

    const vm = new Vue({
        el:'#root',
        data:{
            time:1720589557840,
            msg:'你好'
        },
        computed:{
            fmttime(){
                return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
            }
        },
        methods:{
            getfulltime(){
                return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
            }
        },
        //局部过滤器
        filters:{
            timeFormater(value, str='YYYY-MM-DD HH:mm:ss'){
                return dayjs(value).format(str)
            },

            mySlice(value){
                return value.slice(0,4)
            }
        }
    })

    const vm_1 = new Vue({
        el:'#root_1',
        data:{
            msg:'hello'
        }

    })
</script>
```

## 内置指令

- **v-bind：单向绑定解析表达式，可简写为 :xxx**
- **v-model：双向数据绑定**
- **v-for：绑定数组/对象/字符串**
- **v-on：绑定监听事件，简写为@**
- **v-if：条件渲染（动态控制节点是否存在）**
- **v-else：条件渲染（动态控制节点是否存在）**
- **v-show：条件渲染（动态控制节点是否展示）**
- **v-text**
  - 向其所在的节点中渲染文本内容
  - 与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会

```html
<div id="root">
    <div v-text="name">你好</div>
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data:{
            name:'NUIST'
        }

    })
</script>
```

- **v-html**

  1. 向指定节点中渲染包含htm结构的内容
  2. 与插值语法的区别
     - v-html会替换掉节点中所有的内容，{{xx}}则不会
     - v-html可以识别html结构
  3. <font color=red>v-html有安全性问题</font>
     - <font color=red>在网站上动态渲染任意html是非常危险的，容易导致XSS攻击</font>
     - <font color=red>一定要在可信的内容上使用v-html，永远不要在用户提交的内容上</font>

  ```html
  <div id="root">
      <div v-html="str"></div>
  </div>
  <script type="text/javaScript">
      Vue.config.productionTip = false
      const vm = new Vue({
          el:'#root',
          data:{
              name:'NUIST',
              str:'<h3>你好</h3>'
          }
  
      })
  </script>
  ```

- **v-clock(没有值)**

  1. 本质是一个特殊属性，vue示例创建完毕并接管容器后，会删除v-clock属性
  2. 使用css配合v-clock可以解决网速慢时页面展示出{{xxx}}的问题

- **v-once**
  1. v-once所在节点在初次动态渲染以后，就视为静态内容了
  2. 以后数据的改变不会引起v-once所在结构的更新，可用于性能优化

```html
<div id="root">
    <h2 v-once>初始化的值{{n}}</h2>
    <h2>当前的值{{n}}</h2>
    <button @click="n++">加一</button>
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data:{
            n:20
        }

    })
</script>
```

- **v-pre**
  1. 跳过其所在的节点的编译过程
  2. 可以用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译

## 自定义指令

1. 定义语法

   - 局部指令：

     new Vue({

     ​	directives:{指令名：配置对象}

     })

     new Vue({

     ​	directives(){}

     })

   - 全局指令

     Vue.firective(指令名，配置对象) 或 Vue.directive(指令名，回调函数)

2. <font color=red>配置对象中常用的指令</font>

   - .bind：指令与元素成功绑定时调用
   - .inserted：指令所在元素被插入页面时被调用
   - .update：指令所在模板结构被重新解析时调用

3. 指令名定义时不加v-，使用时加

   指令名如果时多个单词，要使用kebab-case命名方式，不要用camelCase命名

```html
<!--
    1.v-big指令，和v-text指令类似，但会把绑定的数值方法10倍
    2.定义一个v-fbind指令，和v-bind指令类似，但可以让其它所绑定的input元素默认获得焦点
-->
<div id="root">
    <h2>当前值为:<span v-text="n"></span></h2>
    <h2>放大的值为:<span v-big="n"></span></h2>
    <!-- <h2>放大的值为:<span v-big-number="n"></span></h2> -->
    <button @click="n++">点我加一</button>
    <hr/>
    <input type="text" v-fbind:value="n">
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false

    //全局
    Vue.directive('fbind', {
                //指令与元素成功绑定（一上来）
                bind(element, binding){
                    element.value = binding.value
                },
                //指令所在元素被插入页面
                inserted(element, binding){
                    element.focus()
                },
                //指令所在模板被重新解析
                update(element, binding){
                    element.value = binding.value
                },
            })

    const vm = new Vue({
        el:'#root',
        data:{
            n:1,
        },

        directives:{
            "big-number"(element, binding) {
                element.innerText = binding.value * 10
            },
            // big函数何时被调用？1.指令与元素成功绑定（一上来） 2.指令所在的模板被重新解析时
            big(element, binding) {
                console.log(this)  //这里的this是window
                element.innerText = binding.value * 10
            },
            fbind:{
                //指令与元素成功绑定（一上来）
                bind(element, binding){
                    element.value = binding.value
                },
                //指令所在元素被插入页面
                inserted(element, binding){
                    element.focus()
                },
                //指令所在模板被重新解析
                update(element, binding){
                    element.value = binding.value
                }

            }
        }
    })
</script>
```

## 生命周期

<img src="E:\Typora_Image\lifecycle.png" alt="lifecycle" style="zoom: 35%;" />

将要创建 ===> 调用beforeCreate函数

创建完成 ===> 调用created函数

将要挂载 ===> 调用beforeMount函数

挂载完毕 ===> 调用mounted函数 （重要）

将要更新 ===> 调用beforeUpdate函数

更新完毕 ===> 调用updated函数

将要销毁 ===> 调用 beforeDestroy函数 （重要）

销毁完毕 ===> 调用destroyed函数

```html
<div id="root">
    <h2 :style="{opacity}">Hello Vue</h2>
    <button @click="opacity=1">透明度设置为1</button>
    <button @click="stop">点我停止变换</button>
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el:'#root',
        data:{
            opacity:1
        },
        methods: {
            stop(){
                this.$destroy()
            }
        },

        beforeCreate() {
            console.log('beforeCreate')
        },
        created() {
            console.log('created')
            console.log(this)

        },
        mounted() {
            console.log('mounted', this)
            this.timer = setInterval(() => {
                console.log(setInterval)
                this.opacity -= 0.01
                if(this.opacity <=0 ) this.opacity=1     
            }, 16);

        },
        beforeDestroy() {
            console.log('即将销毁')
            clearInterval(this.timer)
        },
    })
</script>
```

常用的生命周期钩子：

1. mounted 发送AJax请求、启动定时器、绑定定时器、订阅消息等初始化操作
2. beforeDestroy 清除定时器、解绑定时器、取消订阅消息等收尾工作

关于销毁ue实例

1. 销毁后借助Vue开发者工具看不到任何消息
2. 销毁后自定义事件会消失，但原生DOM事件依然有效
3. 一般不会在beforeDestroy中操作数据，因为即便操作数据，也不会再触发更新流程了



# Vue组件化编程

## 模块与组件、模块化与组件化

模块

1. 理解：向外提供特定功能的js程序，一般就是一个js文件

2. 为什么：js中的文件很多很复杂
3. 作用：复用js，简化js的编写，提高js运行效率

组件

1. 理解：实现应用中<font color=red>**局部**</font>功能<font color=red>**代码**</font>和<font color=red>**资源**</font>的集合
2. 为什么：一个界面的功能很复杂
3. 作用：复用编码，简化项目编码，提高运行效率

![image-20240715184458274](E:\Typora_Image\image-20240715184458274.png)

## 非单文件组件

一个文件中包含n个组件

Vue使用组件的三大步骤

一、定义组件（创建组件）

二、注册组件

三、使用组件（写组件标签）



<font color=red>**如何定义一个组件？**</font>

使用Vue.extend(options)创建，其中options和new Vue(options)时传入的那个options几乎一样，但也有区别

1. <font color=red>el不要写</font>，最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器
2. <font color=red>data必须写成函数</font>，避免组件被复用的时，数据存在引用关系



<font color=red>**如何注册一个组件**</font>

1. 局部注册：靠new Vue的时候传入components选项
2. 全局注册：靠Vue.component('组件名'，组件)



<font color=red>**编写组件标签**</font>

​	<school></school>

```html
<div id="root">
    <hello></hello>
    <!--编写组件标签-->
    <school></school>
    <hr>
    <student></student>
</div>

<div id="root2">
    <hello></hello>
</div>

<script type="text/javaScript">
    Vue.config.productionTip = false
    // 第一步 创建school组件
    const school = Vue.extend({
        template:`
        <div>
            <h2>学校名称:{{schoolName}}</h2>
            <h2>学校地址:{{add}}</h2>
        </div>
        `,
        data() {
            return {
            schoolName:'NUIST',
            add:'PUK',


            }
        }
    })

    // 第一步 创建student组件
    const student = Vue.extend({
        template:`
                <div>
                    <h2>学生姓名:{{stuName}}</h2>
                    <h2>学生年龄:{{age}}</h2>
                </div>
            `,
        data() {
            return {
            stuName:'wang',
            age:18,
            }
        }
    })

    const hello = Vue.extend({
        template:`
        <div>
            <h2>你好{{name}}</h2>
        </div>
        `,
        data(){
            return {
                name:"wangliang"
            }
        }
    })

    //全局注册组件
    Vue.component('hello', hello)

    new Vue({
        el:'#root',
        //第二步注册（局部注册）
        components:{
            school:school,
            student:student
        }
    })


    new Vue({
        el:'#root2'
    })
</script>
```

<font color=red>几个注意点</font>

1. 关于组件名：

   一个单词组成：

   ​	第一种写法首字母小写：school

   ​	第二种写法首字母大写：school

   多个单词组成：

   ​	第一种写法：my-school

   ​	第二种写法：MySchool

   备注：

   ​	组件名中尽可能回避HTML中已有的元素名称，例如：h2，H2

   ​	可以使用name配置项指定组件在开发者中呈现的名字

2. 关于组件标签

   第一种写法：<school></school>

   第二种写法：<school/>

   备注：<font color=red>**不使用脚手架时，<school/>会导致后续组件不能渲染**</font>

3. 一个简写方式

   const school = Vue.extend(options) 可简写为：const school = options

## 组件嵌套

```html
<div id="root">
    <app></app>
</div>
<script type="text/javaScript">
    Vue.config.productionTip = false

    const student = Vue.extend({
        template:`
        <div>
            <h2>学生名:{{stuName}}</h2>
            <h2>年龄:{{age}}</h2>
        </div>
        `,
        data(){
            return {
                stuName:'wang',
                age:18
            }
        }
    })

    const school = Vue.extend({
        template:`
        <div>
            <h2>学校名:{{schoolName}}</h2>
            <h2>地址:{{add}}</h2>
            <student></student>
        </div>
        `,
        data(){
            return {
                schoolName:'NUIST',
                add:'PKU'
            }
        },
        components:{
            student:student
        }
    })

    const hello = Vue.extend({
        template:`
        <h1>欢迎{{msg}}</h1>
        `,
        data(){
            return {
                msg:"wangliangliang"
            }
        }
    })

    const app = Vue.extend({
        template:`
        <div>
            <school></school>
            <hello></hello>
        </div>
        `,
        components:{
            school,
            hello,
        }
    })


    new Vue({
        el:'#root',
        components:{
          app
        }
    })
</script>
```

## VueComponent

1. school本质是一个名为VueComponent的构造函数，不是程序员定义的。是Vue.extend生成的
2. 我们只需要写<school/>或<school></school>，Vue会解析时帮我们创建school组件的实例对象，即Vue帮我们执行的new VueComponent(options)
3. 特别注意：<font color=red>**每次调用Vue.extend，返回的是一个全新的VueComponent**</font>
4. 关于this的指向：
   - 组件配置中：data函数、menthods函数、watch中的函数、computed中的函数他们均是<font color=red>**VueComponent实例对象**</font>
   - .new Vue()配置中：data函数，methods函数、watch函数、computed函数他们均是<font color=red>**Vue实例对象**</font>

## 一个重要的内置关系

```vue
VueComponent.prototype.__proto__===Vue.prototype
```

<font color=red>为什么要有这个关系</font>

让组件实例对象vc可以访问到Vue原型上的属性方法

脚手架index.html

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <!--针对浏览器的一个特殊配置，让IE浏览器以最高的渲染级别渲染页面-->
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <!--开启移动端的理想视口-->
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <!--配置页签图标-->
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <!--配置网页标题-->
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <!--当浏览器不支持js时noscript中的元素会被渲染-->
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <!--容器-->
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

## 关于不同版本的vue

1. vue.js与vue.runtime.xxx.js的区别
   - Vue.js是完整版的Vue包含核心功能+模板解析器
   - Vue.runtime.xxx.js是运行版的Vue只包含核心功能，没有模板解析器
2. 因为Vue.runtime.xxx.js没有模板解析器，所以不能用template配置，需要使用render函数接收到createElement函数去指定具体内容

## 单文件组件

一个文件中只包含1个组件



## ref属性

1. 用来给元素或者子组件注册引用信息（代替ID）
2. 应用在html标签上获取真实的DOM元素，应用在组件标签上是组件实例对象
3. 使用方式：this.$refs.XXX

## props属性

让组件接收来自外部的数据、

1. 传递数据：<Demo name='XXX'>
2. 接收数据

```vue
<template>
    <div>
        <Student stuName='wangliang' sex='men' :age="18"/>
         <hr/>
        <Student stuName='wangliangliang' sex='men' :age="18"/>
        <hr/>
        <Student stuName='wangliang' sex='men' :age="18"/>
    </div>
</template>

<script>
export default {
    name:'Student',
    data(){
        return {
            msg:'Hello Vue'
        }
    },
    // props:['stuName', 'sex', 'age'] 简单声明接收

    //限制类型
    // props:{
    //     stuName:String,
    //     age:Number,
    //     sex:String
    // }

     props:{

        stuName:
        { 
            type:String, //类型
            required:true //是必要的
        },
        age:{
            type:Number,
            default:99  //默认的
        },
        sex:{
            type:String,
            required:true //是必要的
        }
    }

}
</script>
```

3. <font color=red>备注</font>

   props是只读的，Vue底层会监测对props的修改，如果进行了修改会有警告，若需要修改，复制props中的内容到data中，修改data中的数据

## Mixing混入

```javascript
export default mixin 默认暴露

分别暴露 这样引入 import {mixin} from '../mixin'
export const mixin = {
    methods: {
        showName(){
            alert(this.name)
        }
    }
}
```

功能：可以把多个组件共用的配置提取成一个混入对象

使用方式：

 1. 定义混合

    ```vue
    {
    	data(){},
    	methods:{}
    }
    ```

 2. 使用混合

    - 全局混入 Vue.mixin()
    - 局部混合 mixing:['xxx']



## 插件

功能：用于增强Vue

本质：包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件的使用者传递的数据

定义插件：

```javascript
export default {
  install (Vue){
			//添加全局过滤器
      Vue.filter('mySlice', function(value) {
          return value.slice(0,5)
      })
			
			//添加全局指令
      Vue.directive('fbind', {
          //指令与元素成功绑定（一上来）
          bind(element, binding){
              element.value = binding.value
          },
          //指令所在元素被插入页面
          inserted(element, binding){
              element.focus()
          },
          //指令所在模板被重新解析
          update(element, binding){
              element.value = binding.value
          },
      })
			
			//配置全局混入
      Vue.mixin({
          data(){
              return {
                  x:100,
                  y:200
              }
          }
      })

      //给Vue原型上添加一个方法（vm和vc都能用了）
      Vue.prototype.hello = ()=>{alert("你好")}

  }   
}

```

使用插件：Vue.use()

Scoped样式

作用：让样式在局部生效

写法：<style scoped>

```
Creating a new branch is quick and simple.
```
