#一，Class 与 Style 绑定

##1.1 绑定 HTML Class `v-bind:class`

### 对象语法

      <div id="example">
        <div v-bind:class="{ active: isActive, 'text-danger': hasError }">1</div>
     </div>
     
     var vm = new Vue({
         
         el: "#example",
         data: {    
           isActive: true,
           hasError: false
         }
       
     })
     
     // 渲染为
           <div id="example">
             <div class="active">1</div>
           </div>
     
可以传给 `v-bind:class` 多个对象，通过设置 `isActive` 和 `hasError` 键值为 `true` 或 `false`，来控制 class 列表。例如：`hasError: true` 时，`<div class="active text-danger">1</div>`

也可以直接绑定对象，得到结果和上面一样。

        <div id="example">
            <div  v-bind:class="classObject">12</div>
        </div>
        
        var vm = new Vue({
        
                el: "#example",
                data: {
                    classObject: {
                        active: true,
                        'text-danger': false
                    }
                }
        
            })

也可以绑定对象返回的计算属性

    <div v-bind:class="classObject"></div>
    
    var vm = new Vue({

        el: "#example",
        data: {
            isActive: true,
            error: false
        },
        computed: {
            classObject: function () {
                return {
                    active: this.isActive && !this.error,
                    'text-danger': this.error && this.error.type === 'fatal'
                }
            }
        }

    })
    
### 数组语法

可以把一个数组传给 `v-bind:class`。


    <div id="example">
         <div  v-bind:class="[{active2: isActive},activeClass, errorClass]">hello world</div>
    </div>
  
    var vm = new Vue({    
         el: "#example",
            data: {
                activeClass: 'active',
                errorClass: 'text-danger',
                isActive: true
            }
    
        })
    // 渲染后   
    
    <div class="active2 active text-danger">hello world</div>
    
### 用在组件上

##1.2 绑定内联样式 `v-bind:style`

### 对象语法

`v-bind:style` 的对象语法十分直观——看着非常像 CSS ，其实它是一个 JavaScript 对象。 CSS 属性名可以用驼峰式（camelCase）或短横分隔命名（kebab-case）
    
    <div id="example">
        <div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}">hello vue</div>
    </div>
        
    var vm = new Vue({

        el: "#example",
        data: {
            activeColor: 'red',
            fontSize: 30
        }
    })
    
直接绑定到一个样式对象通常更好，模板更清晰：

    <div id="example">
            <div v-bind:style="styleObject">hello vue</div>
            
    </div>
    
    var vm = new Vue({

        el: "#example",
        data: {
            styleObject: {
                color: 'red',
                fontSize: '30px'
            }
        }

    })
    
###数组语法

v-bind:style 的数组语法可以将多个样式对象应用到一个元素上

    <div v-bind:style="[baseStyles, overridingStyles]">
