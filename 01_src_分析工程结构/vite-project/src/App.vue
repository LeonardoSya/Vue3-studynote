<template>
  <h1>我是App组件</h1>
  <h2>{{ name }}</h2>
  <h2>{{ age }}</h2>
  <button @click="sayHello">sayHello</button>
  <button @click="changeInfo">修改你的信息</button>
  <h3>工作：工种{{ job.type }} 工薪{{ job.salary }}</h3>
</template>

<script eslint-disable>
import { h } from 'vue';

import { ref } from 'vue';    // reference引用 implement实现

export default {
  name: 'App',   // 暂时不考虑响应式的问题

  // 组件中所用到的数据、方法等等 都要配置在setup中
  setup: function () {    // setup不能是一个async函数，因为返回值不再是return的对象，而是promise包裹的对象，模板看不到return对象中的属性 
    // data
    let name = ref('zyyy')   // 对于基础数据类型， ref() 把数据变成响应式数据，返回一个【引用对象】(RefImpl)，原理是Object.defineProperty的get,set(数据劫持)
    let age = ref(18)
    let job = ref({      // 而处理对象类型时， ref()返回的引用对象RefImpl中的value值是一个Proxy代理对象，包含type和salary(对象参数的键值)
      type: '前端',
      salary: '30k',
    })

    // methods
    function sayHello() {
      alert(`Hello,${name.value}`)
    }

    function changeInfo() {
      name.value = 'qja'
      // console.log(job.value);  // Proxy{...}
      job.value.type = '后端'
    }

    // 1.返回一个对象(常用)
    //  如果setup返回的是对象，则对象中的属性方法在模板均可直接使用
    return {
      name,
      age,
      sayHello,
      changeInfo,
      job,
    }

    // 2. 返回一个函数(渲染函数)
    // 渲染函数把h函数调用的返回值交出去
    // return () => h('h1', 'hello world!')
  }
}

</script>

