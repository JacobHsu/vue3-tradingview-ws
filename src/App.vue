<template>
  <div>
    <KLineHeader
      :symbol="symbol"
      :symbolList="symbolList.value"
      @symbolHanlder="symbolHanlder"
    />
    <el-main v-if="symbol">
      {{symbolInfo}}
    </el-main>
  </div>
  <!-- <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/> -->
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import KLineHeader from '@/components/KLineHeader'
import { ref, onMounted, reactive } from 'vue'
import { getSymbols } from '@/api'
import { ws } from '@/utils/socket'
export default {
  name: 'App',
  components: {
    KLineHeader,
    // HelloWorld
  },
  setup() {
    const symbolList = reactive({})
    const symbol = ref('')
    const symbolInfo = ref({})
    const kLineRef = ref(null)
    onMounted(async () => {
      ws.initWebSocket()
      const [list, symbolData] = await getSymbols()
      symbolList.value = list
      symbol.value = symbolData
      symbolInfo.value = list[0]
    })
    const symbolHanlder = (e) => {
      symbol.value = e
      kLineRef.value.setSymbol(e)
    }

    return {
      symbol,
      symbolList,
      symbolInfo,
      symbolHanlder,
      kLineRef,
    }
  },
}
</script>

<style scoped>
  ::v-deep(.el-main) {
    padding: 0;
  }
</style>