<!--
 * @Author: LeonardoSya 2246866774@qq.com
 * @Date: 2023-09-02 14:12:40
 * @LastEditors: LeonardoSya 2246866774@qq.com
 * @LastEditTime: 2023-10-10 16:13:59
 * @FilePath: \Vue\02_src_reactive函数\vite-project\src\App.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<script lang="ts">

import { reactive } from 'vue';

export default {
  name: 'App',

  setup() {

    // const 代理对象 = reactive(源对象), 返回一个代理对象(Proxy的实例对象)
    let person = reactive({
      job: {
        type: '前端',
        salary: '30k',
      },
      hobby: ['linear algebra', 'japanese']
    })


    function changeInfo() {
      // console.log(job);  // Proxy{...}
      person.job.type = '后端'
      person.job.salary = '40k'
      person.hobby[1] = 'gis'
    }

    function add() {
      person.sex = '男'
    }

    function del() {
      person.sex =''
    }
    return {
      person,
      changeInfo,
      add,
      del
    }

  }
}
</script>

<template>
  <div>
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
  </div>

  <h3>ref函数: 定义一个对象类型的响应式数据 定义基本类型数据</h3>
    <h3>ref原理:  Object.defineProperty()实现响应式(数据劫持)</h3>
  <h2>reactive函数: 定义一个对象类型的响应式数据, 返回一个代理对象(Proxy对象)  定义对象/数组类型数据</h2>
    <h2>reactive原理: Proxy实现响应式(数据劫持), 并通过Reflect操作源对象内部的数据</h2>

  <h3>{{ person.job.type }} {{ person.job.salary }} {{ person.hobby }}</h3>
  <button @click="changeInfo">changeInfo</button>


  <button @click="add">add</button>
  <button @click="del">del</button>
  <h3 v-show="person.sex">{{ person.sex }}</h3>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
