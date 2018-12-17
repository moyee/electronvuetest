<template>
    <div id="app">
        <!-- <router-view></router-view> -->
        <button @click="autoUpdate()">获取更新</button>
        <ol id="content">
            <li>生命周期过程展示</li>
            <li v-for="(item, index) in updateStatus" :key="index">{{item}}</li> 
        </ol>
    </div>
</template>

<script>
import { ipcRenderer } from 'electron'
export default {
  data () {
    return {
      appVersion: '',
      updateStatus: []
    }
  },
  mounted () {
    ipcRenderer.on('message', (event, {message, data}) => {
      let msg = message + ' <br>data:' + JSON.stringify(data) + '<hr>'
      this.updateStatus.push(msg)
      if (message === 'isUpdateNow') {
        if (confirm('是否现在更新？')) {
          ipcRenderer.send('updateNow')
        }
      }
    })
  },
  methods: {
    autoUpdate () {
      const ipcRender = this.$electron.ipcRenderer
      ipcRender.send('update')
      ipcRender.on('autoUpdateStatus', (event, res) => {
        this.updateStatus.push(res)
      })
    }
  }
}
</script>


<style>
  /* CSS */
</style>
