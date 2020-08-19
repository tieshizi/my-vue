##----------------------------->>  vue 尤雨溪 民族骄傲


#####vue起始

#cnpm install -g live-server  
开发级的web服务器,live-server,把项目在浏览器打开起一个服务IP是127.0.0.1:8080

#npm init  
初始下前端项目

#实例化 （vue生成器）

<div id='app'>{{message}}</div>  <!-- 模板的输出方式 -->
var app = new Vue({
    el: '#app',
    data: {
        message: 'hello'
    }
})




#### vue内部指令

#v-if  v-else  v-show

<div id='app'>
    <div v-if='isLogin'>郝可可</div>
    <div v-else='isLogin'>请登录</div>
    <div v-show='isLogin'>郝可可</div>
</div>
v-show是css操作，v-if是判断是否加载，v-else和v-if并列只显示一个


#v-for

<ul>
    <li v-for="(item,index) in sortdata2">{{index+1}}:{{item.name}}---------{{item.age}}</li>
</ul>
computed: {
    //在输出data之前进行的编程
    //为了保护data1 所以不能直接return 操作this.data,需要赋值给sortdata1
    sortdata1: function () {
        return this.data1.sort(doData1)
    },
    sortdata2: function () {
        return this.data2.sort(doData2)
    }
}
function doData1(a, b) {
    return a - b;
}


#v-text&v-html

<h3>{{message}}</h3>这种方法是不友好的，网速慢或者报错给用户看到的就是'{{messge}}',所以尽量用v-text
v-html可以解析标签但是不安全，特别是处理表单时

<h3 v-text='message'></h3>
<div v-html='message1'></div>
data: {
        message: '我是可可',
        message1: '<a href=#>我是郝可可</a>',
}


#v-on 绑定监听事件

<div id="app">
    总分数是：{{num}}
    <div>
        <input type="text" v-on:keyup.enter='onEnter' v-model='num1'>
        <br>
        <button v-on:click='onPlus'>加分</button>
        <button @click='onMinus'>减分</button>
    </div>
</div>
data: {
        num: 0,
        num1: 1,
        },
    //构造器一个新属性，可以写很多方法
    methods: {
        onPlus: function () {
            this.num++
        },
        onMinus: function () {
            this.num--
        },
        onEnter: function () {
            this.num = this.num + parseInt(this.num1)
        }
}
    

#v-model 绑定数据源头

数据源双向绑定,多用于表单
-修饰符v-model.lazy（鼠标焦点离开才生效）   v-model.number（只绑定数字，除了字符串开始）    v-model.trim（去除两端空格）
<h3>文本框</h3>
<p>{{message}}kk</p>
<p><input type="text" v-model='message'></p>
<p><input type="text" v-model.lazy='message'></p>
<p><input type="text" v-model.number='message'></p>
<p><input type="text" v-model.trim='message'></p>
<hr>
<h3>文本域</h3>
<textarea v-model='message' cols="30" rows="10"></textarea>
<hr>
<h3>多选框绑定一个值</h3>
<input type="checkbox" id="isTrue" v-model='isTrue'>
<label for="isTrue">{{isTrue}}</label>

<h3>多选框选多个值</h3>
{{mybox}}
<p>
    <input type="checkbox" value='可可' id="kk" v-model='mybox'>
    <label for="kk">kk</label>
    <input type="checkbox" value='耀志' id="yz" v-model='mybox'>
    <label for="yz">yz</label>
    <input type="checkbox" value='郝可' id="hk" v-model='mybox'>
    <label for="hk">hk</label>
</p>
<h3>单选框</h3>
{{myradio}}
<p>
    <input type="radio" value='男' id='one' v-model='myradio'>
    <label for="one">男</label>
    <input type="radio" value='女' id='two' v-model='myradio'>
    <label for="two">女</label>
</p>


#v-bind  绑定标签属性（很重要）

<p><img v-bind:src="imgSrc" width='200px'></p>
<p><a :href="myHref" target='_blank'>跳出去</a></p>
<!-- v-bind:XXX 缩写:XXX -->
<hr>
<div :class='className'>1绑定class</div>
<hr>
<div :class='{classA:isOk}'>2单选框判断某一属性值绑定class</div>
<p>
    <input type="checkbox" id='red' value='true' v-model='isOk'>
    <label for="red">{{isOk}}</label>
</p>
<div :class='isOk?classA:classB'>3三元表达式绑定class</div>
<hr>
<div :class='[classA,classC]'>4用数组绑定两个class</div>
<div :style='myStyle'>5用对象绑定style</div>
data: {
    imgSrc: 'https://i0.hdslb.com/bfs/face/b8151976d1335b63922129f0b5af5ae884c7a4e5.jpg@150w_150h.jpg',
    myHref: 'https://wwww.baidu.com',
    className: 'classA',
    isOk: false,
    classA: 'classA',
    classB: 'classB',
    classC: 'classC',
    myStyle: {
        color: 'hotpink',
        fontSize: '12px',
    }
},


#别的指令v-pre  v-once  v-cloak
v-pre 不作为模板输出
v-cloak 页面加载完才显示
v-once  不去进行双向绑定




###&&##多学的知识点
assets 文件夹在linux nnux系统下是不编译的


