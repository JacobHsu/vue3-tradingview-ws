<template>
  <div>
    <KLineHeader
      :symbol="symbol"
      :symbolList="symbolList.value"
      @symbolHanlder="symbolHanlder"
    />
  </div>
  <!-- <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/> -->
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import KLineHeader from '@/components/KLineHeader'
import { ref, onMounted, reactive } from 'vue'
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

    const options = [
      {
        value: 'Option1',
        pair: 'Option1',
      },
      {
        value: 'Option2',
        pair: 'Option2',
      },
      {
        value: 'Option3',
        pair: 'Option3',
      },
      {
        value: 'Option4',
        pair: 'Option4',
      },
      {
        value: 'Option5',
        pair: 'Option5',
      },
    ]
      symbolList.value = options
      symbol.value = 'symbol'
      symbolInfo.value = options[0]
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

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
