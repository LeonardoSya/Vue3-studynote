### 声明式地描述UI
前端页面涉及这些内容：DOM元素、属性(如id,class)、事件、元素的层级结构(DOM树的层级结构,既有子节点又有父节点)

Vuejs3如何声明式地描述上述内容：
    DOM元素和属性和层级结构与HTML标签一致
    使用 : 或v-bind来描述动态绑定的属性如<div :id='dynamicId'></div>
    使用 @ 或v-on描述事件<div @click='handler'></div>
    用户不需要手写任何命令式代码，这就是声明式地描述UI

上述是用模板描述UI，Vue3还支持用虚拟DOM描述UI，这种方式更灵活
```js
        const title= {
            tag:'h1',   // 标签名
            props:{   // 属性
                onClick: handler
            },
            children: [  // 子节点
                {tag:'span'}
            ]
        }
```

### 初识渲染器
一个组件要渲染的内容是通过渲染函数来描述的，Vuejs会根据组件的render函数的返回值拿到虚拟DOM，然后就可以把组件的内容渲染出来了
虚拟DOM：用js对象来描述真实的DOM结构
虚拟DOM如何变成真实DOM 并渲染到浏览器页面中？  *渲染器*

编写一个渲染器
```js
    function renderer(vnode,container) {
        // 使用vnode.tag作为标签名创建DOM元素
        const el=document.createElement(vnode.tag)
        // 遍历vnode.props，将属性和事件添加到DOM元素
        for(const key in vnode.props) {
            if(/^on/.test(key)) {  // 如果key以on开头，说明它是事件
                el.addEventListener(key.substr(2).toLowerCase(), vnode.prop[key])  // 事件名称OnClick -> click   第二个参数是事件处理函数
                
            }
        }

        // 处理children
        if(typeof vnode.children === 'string'){
            // 如果children是字符串，说明它是元素的文本子节点
            el.appendChild(document.createTextNode(vnode.children))
        } else if(Array.isArray(vnode.children)) {
            // 递归地调用renderer函数渲染子节点，使用当前元素el作为挂载点
            vnode.children.forEach(child => renderer(child,el))
        }

        // 将元素添加到挂载点下
        container.appendChild(el)
    }
```
这里的renderer接受两个参数：vnode(虚拟DOM对象)、container(一个真实DOM元素，作为挂载点，渲染器会把虚拟DOM渲染到该挂载点下)

### 组件的本质
虚拟DOM其实就是用来描述真实DOM的普通js对象

而组件就是一组DOM元素的封装，这组DOM元素就是组件要渲染的内容。因此可以定义一个函数代表组件，函数返回值代表组件要渲染的内容
```js
    const MyComponent = function () {
        return {
            tag:'div',
            props: {
                onClick:()=>alert('hello')
            },
            children:'click me'  
        } 
    }
```
组件的返回值也是虚拟DOM，它代表组件要渲染的内容
用虚拟DOM描述组件：让虚拟DOM对象中的tag属性来存储组件函数:
```js
const vnode={
    tag: MyComponent  // 描述组件
}
```
此时tag属性不是标签名称而是组件函数，为了能够渲染组件，需要渲染器支持，要修改renderer函数
```js
    function renderer(vnode, container) {
        if(typeof vnode.tag==='string') {
            // 说明vnode描述的是标签元素
            mountElement(vnode, container)
        } else if(typeof vnode.tag=='object') {
            // 如果是对象，说明vnode描述的是组件
            mountComponent(vnode, container)
        }
    }

    function mountElement(vnode,container) {
        const el=document.createElement(vnode.tag)
        for(const key in vnode.prop) {
            if(/^on/.test(key)) {
                el.addEventListener(key.substr(2).toLowerCase(), vnode.props[key]) 
            }
        }
        if (typeof vnode.children==='string') {
            el.appendChild(document.createElement(vnode.children))
        } else if (Array.isArray(vnode.children)) {
            vnode.children.forEach(child=>renderer(child,el))
        }
        container.appendChild(el)
    }

    function mountComponent(vnode,container) {
        // vnode.tag是组件对象，调用它的render函数得到组件要渲染的内容(虚拟DOM)
        const subtree=vnode.tag.render()  // subtree也是虚拟DOM，那么直接调用renderer函数完成渲染即可
        renderer(subtree,container)
    }
```

### 模板工作原理
编译器和渲染器都只是一段程序,编译器的作用：将模板渲染编译为渲染函数
```js
<template>...</template>    // template标签里的内容就是模板内容，编译器会把它们编译成渲染函数并添加到script标签块的组件对象上
<script> 
    export default {
        data(){...},
        methods:{...}
    }
</script> 
```
实际在浏览器中运行的代码:
```js
export default {
    data(){},
    methods:{...},
    render() { 
        return h('div',{onClick:'handler'}, 'click me')
    }
}

```
因此，无论是使用模板还是直接手写渲染函数，对于一个组件来说，它要渲染的内容最终都是通过渲染函数产生，然后*渲染器*再把渲染函数返回的虚拟DOM渲染为真实DOM
这就是模板的工作原理，也是Vuejs渲染页面的流程

### 总结 Vue.js是各个模块组成的有机整体

组件的实现依赖于*渲染器*，模板的编译依赖于*编译器*

**渲染器工作原理**：递归地遍历虚拟DOM对象，并调用原生DOM API来完成真实DOM的创建。渲染器会通过Diff算法找出变更点，并只会更新需要更新的内容
**组件的本质**：一组虚拟DOM元素的封装，它可以是一个返回虚拟DOM的函数，也可以是一个对象(但这个对象下必须要有一个函数用来产出组件要渲染的虚拟DOM)。渲染器先执行组件的渲染函数并得到其返回值subtree，再递归地调用渲染器将subtree渲染出来
**模板工作原理**：模板会被编译器程序编译为渲染函数

