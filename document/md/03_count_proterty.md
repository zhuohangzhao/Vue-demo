#一，计算属性

## 基础例子
在模板中放入太多的逻辑会让模板过重且难以维护。
    
    <div id="example">
      {{ message.split('').reverse().join('') }}
    </div>
    
所以需要使用计算属性

    <div id="example">
      <p>Original message: "{{ message }}"</p>
      <p>Computed reversed message: "{{ reversedMessage }}"</p>
    </div>
    
    var vm = new Vue({
        el:"#example",
        data: {
            message: "Hello"
        },
        computed: {
           
            reversedMessage: function() {
                     return this.message.split('').reverse().join('')
            }      
        }
        
    })
    // Original message: "Hello"    
    //   Computed reversed message: "olleH"
    
上面代码，`computed` “计算”的意思，里面声明了一个计算属性 `reversedMessage`，这个函数将用作属性 `vm.reversedMessage` 的 `getter`。使 vm 可以直接获取到计算属性，`vm.reversedMessage` 的值始终取决于 `vm.message` 的值。

    console.log(vm.reversedMessage) // -> 'olleH'
    vm.message = 'Goodbye'
    console.log(vm.reversedMessage) // -> 'eybdooG'
    
## 计算缓存 vs Methods
 
      <div id="example">
        <p>Computed reversed message: "{{ reverseMessage() }}"</p>
      </div>
      
      var vm = new Vue({
              el:"#example",
              data: {
                  message: "Hello"
              },
              methods: {
                 reverseMessage: function () {
                 return this.message.split('').reverse().join('')
             }
         } 
      })
      
使用`method`可以达到同样效果，不同的是**计算属性是基于依赖缓存**，计算属性只有在它的相关依赖发生改变时才会重新取值。这就意味着只要 message 没有发生改变，多次访问 `reversedMessage` 计算属性会立即返回之前的计算结果，而不必再次执行函数。而 `method` 每当重新渲染的时候，调用总会执行函数，所以method调用不用缓存。

## 计算 setter

计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter：
    
    // ...
    computed: {
      fullName: {
        // getter
        get: function () {
          return this.firstName + ' ' + this.lastName
        },
        // setter
        set: function (newValue) {
          var names = newValue.split(' ')
          this.firstName = names[0]
          this.lastName = names[names.length - 1]
        }
      }
    }
    // ...
## 观察 watcher

虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的 watcher 。这是为什么 Vue 提供一个更通用的方法通过 watch 选项，来响应数据的变化。当你想要在数据变化响应时，执行异步操作或开销较大的操作，这是很有用的

    <div id="watch-example">
      <p>
        Ask a yes/no question:
        <input v-model="question">
      </p>
      <p>{{ answer }}</p>
    </div>


    <script src="https://unpkg.com/axios@0.12.0/dist/axios.min.js"></script>
    <script src="https://unpkg.com/lodash@4.13.1/lodash.min.js"></script>
    <script>
    
    var watchExampleVM = new Vue({
      el: '#watch-example',
      data: {
        question: '',
        answer: 'I cannot give you an answer until you ask a question!'
      },
      watch: {
        // 如果 question 发生改变，这个函数就会运行
        question: function (newQuestion) {
          this.answer = 'Waiting for you to stop typing...'
          this.getAnswer()
        }
      },
      methods: {
        // _.debounce 是一个通过 lodash 限制操作频率的函数。
        // 在这个例子中，我们希望限制访问yesno.wtf/api的频率
        // ajax请求直到用户输入完毕才会发出
        // 学习更多关于 _.debounce function (and its cousin
        // _.throttle), 参考: https://lodash.com/docs#debounce
        getAnswer: _.debounce(
          function () {
            var vm = this
            if (this.question.indexOf('?') === -1) {
              vm.answer = 'Questions usually contain a question mark. ;-)'
              return
            }
            vm.answer = 'Thinking...'
            axios.get('https://yesno.wtf/api')
              .then(function (response) {
                vm.answer = _.capitalize(response.data.answer)
              })
              .catch(function (error) {
                vm.answer = 'Error! Could not reach the API. ' + error
              })
          },
          // 这是我们为用户停止输入等待的毫秒数
          500
        )
      }
    })
    </script>