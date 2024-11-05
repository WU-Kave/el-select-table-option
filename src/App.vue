<template>
  <h2>parent selectValue: {{ selectValue }}</h2>
  <div class="child-box">
    <h2>child box</h2>
    <BaseTableSelect 
      ref="baseTableSelectRef"
      v-model="selectValue" 
      :options="options" 
      :columnConfig="columnConfig" 
      valueName="pId" 
      keyName="pId"
      @change="handleChange"
    >
    </BaseTableSelect>
  </div>

  <div style="margin-top: 12px;">
    <el-button @click="handleChangeValue">change value</el-button>
    <el-button @click="handleChangeOption">change options</el-button>
    <el-button @click="handleGetFocus">get focus</el-button>
  </div>
</template>
<script setup>
import BaseTableSelect from './components/BaseTableSelect.vue'
import {ref} from 'vue'

const selectValue = ref('546')

const options = ref([
  {pId: "213", department: '古典风格号', createTime: '2024-12-23', place: '学校'},
  {pId: "546", department: '都听好', createTime: '2024-12-23', place: '家里'},
  {pId: "345", department: '按时到岗', createTime: '2024-12-23', place: '医院'}
])

const columnConfig = ref([
  {label: '单号', prop: 'pId'},
  {label: '部门', prop: 'department'},
  {label: '时间', prop: 'createTime'},
  {label: '地点', prop: 'place'},
])

// 测试切换值
const handleChangeValue = () => {
  selectValue.value = '213'
}
// 测试修改options
const handleChangeOption = () => {
  options.value = [
  {pId: "4536", department: 'dshddtyfh ', createTime: '2024-12-23', place: '学校'},
  {pId: "3456", department: 'sdthdb ydfht ', createTime: '2024-12-23', place: '家里'},
]
}
const baseTableSelectRef = ref()
// 测试通过 ref 操作子组件
const handleGetFocus = () => {
  baseTableSelectRef.value.getFocus()
}

const handleChange = (value) => {
  console.log('parent handleChange');
}

</script>

<style lang="scss" scoped>
.child-box {
  margin: 10px;
  margin-bottom: 20px;
  height: 160px;
  background-color: #eee;
  padding: 10px; 
  border-radius: 3px;
  box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.1);
}
</style>
