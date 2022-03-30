<template>
  <el-header>
    <el-select
      v-model="symbol"
      filterable
      :placeholder="'plzChoicePair'"
      @change="changeHallder"
    >
      <el-option
        v-for="item in symbolList"
        :key="item['quote-currency']"
        :label="item.pair"
        :value="item['base-currency'] + item['quote-currency']"
      />
    </el-select>
  </el-header>
</template>
<script>
  import { ElSelect, ElOption } from 'element-plus'
  
  export default {
    name: 'KLineHeader',
    components: { ElSelect, ElOption },
    props: {
      symbol: {
        type: String,
        required: true,
      },
      symbolList: {
        type: Array,
        required: true,
      },
    },
    emits: ['symbolHanlder'],
    setup(props, { emit }) {
      const changeHallder = (e) => emit('symbolHanlder', e)
      return {
        changeHallder,
      }
    },
  }
</script>
<style scoped>
  .el-header {
    background: #131625;
    display: flex;
    padding: 15px;
    align-items: center;
    border-bottom: 1px solid black;
  }
  ::v-deep(.el-input__inner) {
    background-color: #21253c;
    color: #f8f8f8;
    border-color: #000;
  }
</style>
