#一，列表渲染

##1.1 `v-for`

们用 `v-fo` 指令根据**一组数组**的选项列表进行渲染。 `v-for` 指令需要以 `item in items` 形式的特殊语法， `items` 是源数据数组并且 `item` 是数组元素迭代的别名。
        
        <ul id="example-1">
            <li v-for="item in items">
                {{item.message}}
            </li>
        </ul>
        
        var vm = new Vue({
        
            el: "#example-1",
            data: {
                items:[
                    {message: 'Foo' },
                    {message: 'Bar' }
                ]
            }
        
        })
结果：
 - Foo
 - Bar
 
 在 `v-for` 块中，我们拥有对父作用域属性的完全访问权限。 `v-for` 还支持一个可选的第二个参数为当前项的索引`index`
 
        <ul id="example-1">
            <li v-for="(item,index) in items">
                {{message}}-{{ index }}-{{item.message}}
            </li>
        </ul>
        
        var vm = new Vue({
    
            el: "#example-1",
            data: {
                message: 'parent',
                items:[
                    {message: 'Foo' },
                    {message: 'Bar' }
                ]
            }
    
        })

结果：
 - parent-0-Foo
 - parent-1-Bar
 
注意：`of` 可以替代 `in`，最原生的迭代器（`for...of`遍历）


### `Template-v-for`
可以用带有 v-for 的 <template> 标签来渲染多个元素块

    <ul>
      <template v-for="item in items">
        <li>{{ item.msg }}</li>
        <li class="divider"></li>
      </template>
    </ul>

### 对象迭代 `v-for`
可以用 v-for 通过一个对象的属性来迭代

    <ul id="repeat-object" class="demo">
        <li v-for="value in object">
            {{ value }}
        </li>
    </ul>
    
    var vm = new Vue({

        el: '#repeat-object',
        data: {
            object: {
                FirstName: 'John',
                LastName: 'Doe',
                Age: 30
            }
        }

    })

结果：
 - John
 - Doe
 - 30

可以提供第二个参数为**键名**
    
    <ul id="repeat-object" class="demo">
        <li v-for="(value, key) in object">
           {{ key }}:{{ value }}
        </li>
    </ul>        
    
第三个参数为**索引**    
    
    <ul id="repeat-object" class="demo">
        <li v-for="(value, key, index) in object">
           {{index}}.{{ key }}:{{ value }}
        </li>
    </ul>


### 组件和 `v-for`

##1.2 `key`

为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有唯一 id。这个特殊的属性相当于 Vue 1.x 的 track-by ，但它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值（在这里使用简写）：
    
    <div v-for="item in items" :key="item.id">
      <!-- 内容 -->
    </div>

##1.3 数组更新检测

##变异方法
变异方法(mutation method)，会改变被这些方法调用的原始数组。
Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：
 - `push()`
 - `pop()`
 - `shift()`
 - `unshift()`
 - `splice()`
 - `sort()`
 - `reverse()`

在控制台输入`vm.items.push({message:"baz"})`
    
    <ul id="example-1">
        <li v-for="item in items">
            {{item.message}}
        </li>
    </ul>
        
    var vm = new Vue({
        
        el: "#example-1",
        data: {
            items:[
                {message: 'Foo' },
                {message: 'Bar' }
            ]
        }
        
    })        

结果：
 - Foo
 - Bar
 - baz
 
## 重塑数组

非变异(non-mutating method)方法，例如： `filter()`, `concat()`, `slice()` 。这些不会改变原始数组，但总是返回一个新数组。当使用非变异方法时，可以用新数组替换旧数组：

    vm.items = vm.items.filter(function(item){
        return item.message.match(/Foo/)
    })
    
结果：
 - Foo
 
 你可能认为这将导致 Vue 丢弃现有 DOM 并重新渲染整个列表。幸运的是，事实并非如此。 Vue 实现了一些智能启发式方法来最大化 **DOM 元素重用**，所以**用一个含有相同元素的数组去替换原来的数组是非常高效的操作**
    
## 注意事项

由于 JavaScript 的限制， Vue 不能检测以下变动的数组：
 1. 当你利**用索引直接设置一个项**时，例如： vm.items[indexOfItem] = newValue
 2. 当你**修改数组的长度**时，例如： vm.items.length = newLength
为了避免第一种情况，以下两种方式将达到像 vm.items[indexOfItem] = newValue 的效果， 同时也将触发状态更新：


     // Vue.set
     Vue.set(vm.items, indexOfItem, newValue)
       
     // Array.prototype.splice
     vm.items.splice(indexOfItem, 1, newValue)
     
     Vue.set(vm.items, 2,{message:"ddd"})  // 增加一项
     vm.items.splice(2, 0,{message:"bbb"}) // 下标为 2 增加一项
结果：
   -  Foo
   -  Bar
   -  ofo
   
避免第二种情况，使用 splice：
    
    vm.items.splice(newLength)


##1.3 显示过滤/排序结果

有时，我们想要显示一个数组的过滤或排序副本，而不实际改变或重置原始数据。在这种情况下，可以创建返回过滤或排序数组的**计算属性**。

    <ul id="example-1">
        <li v-for="n in evenNumbers">{{ n }}</li>
    </ul>
    
    var vm = new Vue({

        el: "#example-1",
        data: {
            numbers: [ 1, 2, 3, 4, 5 ]
        },
        computed: {
            evenNumbers: function () {
                return this.numbers.filter(function (number) {
                    return number % 2 === 0
                })
            }
        }

    })
        
结果：
   - 2
   - 4

或者在计算属性不适用的情况下 (例如，在嵌套 `v-for` 循环中) 使用 `method` 方法：

    <ul id="example-1">
        <li v-for="n in even(numbers)">{{ n }}</li>
    </ul>
        
    var vm = new Vue({

        el: "#example-1",
        data: {
            numbers: [ 1, 2, 3, 4, 5 ]
        },
        methods: {
            even: function (numbers) {
                return numbers.filter(function (number) {
                    return number % 2 === 0
                })
            }
        }

    })
        
  






