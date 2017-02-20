#一，表单控件

##1.1 基础用法

你可以用 `v-model` 指令在**表单控件元素上创建双向数据绑定**。`v-model` 本质上不过是语法糖，它负责监听用户的输入事件以更新数据，并特别处理一些极端的例子。
注意：`v-model` 并不关心表单控件初始化所生成的值。因为它会选择 Vue 实例数据来作为具体的值。


### 文本
    
    <div id="example">
        <input v-model="message" placeholder="edit me">
        <p>Message is: {{ message }}</p>
    </div>
    
    new Vue({
        el: '#example',
        data: {
            message:''
        }
    })
    
![](leanote://file/getImage?fileId=58821379c9740b2d8f000007)
       
### 多行文本

    <div id="example">
        <span>Multiline message is:</span>
        <p style="white-space: pre">{{ message }}</p>
        <br>
        <textarea v-model="message" placeholder="add multiple lines"></textarea>
    </div>
    
    new Vue({
        el: '#example',
        data: {
            message:''
        }
    })
    
### 复选框

    <div id="example">
        <input type="checkbox" id="checkbox" v-model="checked">
        <label for="checkbox">{{ checked }}</label>
    </div>
    
    new Vue({
        el: '#example',
        data: {
            checked:''
        }
    })    
    
    
多个勾选框，绑定到同一个数组

    <div id="example">
        <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
        <label for="jack">Jack</label>
        <input type="checkbox" id="john" value="John" v-model="checkedNames">
        <label for="john">John</label>
        <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
        <label for="mike">Mike</label>
        <br>
        <span>Checked names: {{ checkedNames }}</span>
    </div>

    new Vue({
        el: '#example',
        data: {
            checkedNames: []
        }
    })
    
注意：`input` 里 `value` 的值绑定 `{{checkedNames}}` 的值，而不是 `label` 的文本

### 单选按钮

    <div id="example">
        <input type="radio" id="one" value="One" v-model="picked">
        <label for="one">One</label>
        <br>
        <input type="radio" id="two" value="Two" v-model="picked">
        <label for="two">Two</label>
        <br>
        <span>Picked: {{ picked }}</span>
    </div>    
    new Vue({
        el: '#example',
        data: {
            picked:''
        }
    })    
    
### 选择列表

单选列表

    <div id="example">

        <select v-model="selected">
            <option>A</option>
            <option>B</option>
            <option>C</option>
        </select>
        <span>Selected: {{ selected }}</span>

    </div>
    
    new Vue({
        el: '#example',
        data: {
            selected:''
        }
    })
        
多选列表（绑定到一个数组）

    <div id="example">

        <select v-model="selected" multiple>
            <option>A</option>
            <option>B</option>
            <option>C</option>
        </select>
        <span>Selected: {{ selected }}</span>

    </div>

    new Vue({
        el: '#example',
        data: {
            selected:[]
        }
    })
    
动态选项，用 `v-for` 渲染：

    <div id="example">
        <select v-model="selected">
            <option v-for="option in options" v-bind:value="option.value">
                {{ option.text }}
            </option>
        </select>
        <span>Selected: {{ selected }}</span>
    </div>    

    new Vue({
        el: '#example',
        data: {
            selected: 'A',
            options: [
                { text: 'One', value: 'A' },
                { text: 'Two', value: 'B' },
                { text: 'Three', value: 'C' }
            ]
        }
    })
    
##1.2 绑定 value

对于单选按钮，勾选框及选择列表选项， `v-model` 绑定的 value 通常是静态字符串（对于勾选框是逻辑值）：
注意：`input` 里 `value` 的值绑定 `{{checkedNames}}` 的值，而不是 `label` 的文本

    <!-- 当选中时，`picked` 为字符串 "a" -->
    <input type="radio" v-model="picked" value="a">
    
    <!-- `toggle` 为 true 或 false -->
    <input type="checkbox" v-model="toggle">
    
    <!-- 当选中时，`selected` 为字符串 "abc" -->
    <select v-model="selected">
      <option value="abc">ABC</option>
    </select>
    
但是有时我们想绑定 value 到 Vue 实例的一个**动态属性**上，这时可以用 v-bind 实现，并且这个属性的值可以不是字符串。 
  

###复选框
 
    <input
       type="checkbox"
       v-model="toggle"
       v-bind:true-value="a"
       v-bind:false-value="b"
    >  

    
    // 当选中时
    vm.toggle === vm.a
    // 当没有选中时
    vm.toggle === vm.b
    
### 单选框
 

    <input type="radio" v-model="pick" v-bind:value="a"> 

     // 当选中时
     vm.pick === vm.a
     
### 选择列表设置
  

     <select v-model="selected">
         <!-- 内联对象字面量 -->
            <option v-bind:value="{ number: 123 }">123</option>
     </select>
  
    // 当选中时
    typeof vm.selected // -> 'object'
    vm.selected.number // -> 123  
  
  
##1.3 修饰符
  
### `.lazy`

在默认情况下， v-model 在 input 事件中同步输入框的值与数据 (除了 上述 IME 部分)，但你可以添加一个修饰符 lazy ，从而转变为在 change 事件中同步：

    <!-- 在 "change" 而不是 "input" 事件中更新 -->
    <input v-model.lazy="msg" >

###  `.number`

如果想自动将用户的输入值转为 Number 类型（如果原值的转换结果为 NaN 则返回原值），可以添加一个修饰符 number 给 v-model 来处理输入值：

    <input v-model.number="age" type="number">

这通常很有用，因为在 `type="number"` 时 HTML 中输入的值也总是会返回字符串类型。

### `.trim`

如果要自动过滤用户输入的首尾空格，可以添加 trim 修饰符到 `v-model` 上过滤输入：
    
    <input v-model.trim="msg">
    
##1.4 v-model 与组件    