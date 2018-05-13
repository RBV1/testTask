<template>
  <div id="app">
    <TaskList></TaskList>
  </div>
</template>

<script>
import TaskList from './components/TaskList'

export default {
  name: 'App',
  components: {
    TaskList
  }
}

</script>

<style lang="less">
  @import './assets/style/theme.less';
</style>
