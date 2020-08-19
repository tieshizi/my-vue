###vue.directive 自定义指令
全局api:在构造器外部用vue提供给我们的API函数来定义新的功能。

<div v-keke='color'>{{num}}</div>
Vue.directive('keke', {
    bind: function (el, binding) {
        console.log('被绑定')
        el.style = 'color:' + binding.value
    }, 
    unbind: function () {
        console.log('解绑')
    }
})

<button onclick='myUnbind()'>解绑</button>
function myUnbind() {
        app.$destroy();
}


###vue构造器延伸extend实例
在vue构造器外边扩展一个新的构造器，经常和组件配合使用封装模块，写一个全局扩展之后放到某个组件中，别的组件也可以用

var kekeExtend = Vue.extend({
    template: "<a :href='myUrl'>{{fangxiang}}</a>",
    data: function () {
        return {
            fangxiang: '跳到百度',
            myUrl: 'https://www.baidu.com'
        }
    }
})
// 挂载
new kekeExtend().$mount('#keke')


###Vue全局操作set()
在构造器外面声明数据，赋值给构造器，但是如果是数组，虚拟dom用下标和length改变数组的值的时候，vue(js)是监听不到的，需要用到Vue.set()
function add() {
    app.arr[1] = '8097789798'
    // Vue.set(app.arr, 1, '9999')
}
// 引用构造器外的数据
var outData = {
    count: 1,
    goods: 'car',
    arr: ['aaa', 'bbb', 'ccc']
}
var app = new Vue({
    el: '#app',
    data: outData
})


### vue生命周期

