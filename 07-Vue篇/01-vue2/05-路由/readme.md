## 路由
用 Vue.js + Vue Router 创建单页应用，是非常简单的。使用 Vue.js ，我们已经可以通过组合组件来组成应用程序，当你要把 Vue Router 添加进来，我们需要做的是，将组件（components）映射到路由（routes），然后告诉Vue Router 在哪里渲染它们。
故 Vue Router 的作用，是将组件和url地址对应起来，路径的变化会触发后台路由，所以通过锚点变化对应前台路由。

### 路由的创建

- 在html中`router-link`相当于`a`标签动态的改变锚点值。而`router-view`则类似于一个插入标签，路由通过对不同的锚点值进行解析，从而将不同的组件渲染到该地。
- 在js中对路由创建的步骤分为三步：
  - 第一步：创建模板字面量
```javascript
var bar = {template:'<h1>hello bar</h1>'}
var foo = {template:'<h1>hello foo<h1/>'}
```
  - 第二步：创建路由，以及匹配规则
  ```javascript
  var router = new VueRouter({
    routes:[
      {path:'foo',component:foo},
      {path:'bar',component:bar}
    ]
  })
  ```
  - 第三步：将路由挂载到对应的vue实例当中
```JavaScript
var vm = new Vue({
  el:'#app',
  router:router
})
```

在该步骤中可以通过$mount将DOM节点挂载到Vue实例当中，具体步骤如下：

```javascript
var vm = new Vue({
    router
}).$mount('#app')
```

### 高亮

通过点击按钮会被自动加入 className ，可以给选中的按钮做高亮处理：

```html
// .router-link-active
<a href="#/bar" class="router-link-exact-active router-link-active">go to bar</a>
```

### 重定向

前端的重定向是改变锚点的路径，这个后台服务器301/302重定向是有区别的。

```javascript
{path:'/',redirect:'/foo'}
```

### 动画

动画的效果基本和组件相同，通过在html页面中镶嵌一个`<transition>`标签，然后定义动画的过渡效果。

```html
<!-- html 页面 -->
<transition mode="out-in">
    <touter-view></touter-view>
</transition>
```

```css
.v-enter, .v-leave-to{
    opacity:0;
    transform:translateX(100px);
}
.v-enter-active, .v-leave-active{
    translate:all 0.5s;
}
```

### 查询字符串

#### query

既然是路由那么必定会有查询字符串的处理，在`vue-router`中，通过`$route`能够获取到url的路径处理，打印`$route`便可清晰地看清该对象中的方法属性。

```javascript
//在路由生命周期created中打印一下查询字符串的值 ？id=1
var foo = {
    template:'<h1>hello foo</h1>',
    created(){
        console.log(this.$route.query.id);
    }
}
```

#### params

params和query方法只是url的解析不同，但共同点其实都需要$route这个对象对其进行解析。

```html
<!-- html中 -->
<!-- query -->
<router-link to='/foo?id=1'></router-link>
<!-- params -->
<router-link to='/foo/1'></router-link>
```

```javascript
// js中
// query
{path:'/foo',component:foo}
//params
{path:'/foo/:id',component:foo}
```

### 实现子路由

与路由的区别在于router实例中需要通过children属性对子路由的规则进行制定。

```javascript
//值得注意地是子路由中的路径不需要加/,这是因为/会直接匹配根路径
var router = new VueRouter({
    routes:[
        {
            path:'/account',
            component:account,
            children:[
                {path:'login',component:login},
                {path:'register',component:register}
            ]
        }
    ]
})
```

### 命名视图

其实说白了就是为了让路由在处理一个url路径的时候展示多个组件，这是由于`router-view`本身是没有名字的，当router实例在进行渲染的时候，会将匹配的值渲染进`router-view`中而无法进行自适应。

- 需要注意地是组件的名称会默认加上引号
- 在html中引入的组件名称是字符串，这是因为组件视图中的属性并不是 Vue 指令（如果是Vue指令的话，像这样`:name='left'`才会将left当成一个变量）

```html
<!-- html -->
<router-view></router-view>
<router-view name='left'></router-view>
<router-view name='main'></router-view>
```

```javascript
// js
var router = new VueRouter({
    routes:[
        {
            path:'/',
            components:{
                default:header,
                left:left,
                main:main
            }
        }
    ]
})
```

