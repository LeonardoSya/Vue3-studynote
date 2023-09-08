<!--
 * @Author: LeonardoSya 2246866774@qq.com
 * @Date: 2023-09-02 14:12:40
 * @LastEditors: LeonardoSya 2246866774@qq.com
 * @LastEditTime: 2023-09-09 07:29:28
 * @FilePath: \Vue\02_src_reactive函数\vite-project\src\App.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<script lang="ts">

import { reactive } from 'vue';

export default {
  name: 'App',
  setup() {
    let job = reactive({
      type: '前端',
      salary: '30k'
    })

    function changeInfo() {
      // console.log(job);  // Proxy{...}
      job.type = '后端'
    }

    return {
      job,
      changeInfo
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

  <h3>ref函数: 定义一个对象类型的响应式数据</h3>
  <h2>reactive函数: 定义一个对象类型的响应式数据(基本类型要用ref函数)</h2>

  <h3>{{ job.type }} {{ job.salary }}</h3>
  <button @click="changeInfo">changeInfo</button>
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
