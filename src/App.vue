<template>
  <div id="app">
    <HelloWorld msgInfo="空全局创建系统"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;

  color: #2c3e50;
  width: 290px;
  height: 370px;
  border: solid 2px ;
  top: 50%;
  left: 50%;
  margin-left: -145px;
  margin-top: 100px;
  position: relative;
  text-align: center;
  background-color: rgb(241, 253, 231);
  border-radius: 15px 15px 15px 15px; /* 边框的弧度*/
}
</style>
