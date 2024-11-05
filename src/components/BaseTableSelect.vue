<template>
  <p>child selectValue: {{ selectValue }}</p>
  <el-select 
    ref="inputRef" 
    v-model="selectValue"
    clearable
    filterable
    :placeholder="placeholder" 
    :disabled="disabled" 
    :filter-method="filterMethod" 
    @change="handleChange"
  >
    <ul class="select-ul">
      <li v-for="column in columnConfig" :key="column.label">{{ column.label }}</li>
    </ul>
    <el-option v-for="item in showOptions" :key="item[keyNameCom]" :value="item[valueName]">
      <ul class="select-ul select-ul-data">
        <li v-for="column in columnConfig" :key="column.label">{{ item[column.prop] }}</li>
      </ul>
    </el-option>
  </el-select>
</template>

<script setup>
import { ref, reactive, onMounted, computed, watch, nextTick } from 'vue'

defineOptions({
  name: 'BaseTableSelect'
})

const emit = defineEmits(['update:modelValue', 'keyup-enter', 'focus', 'change'])

const props = defineProps({
  modelValue: null,
  disabled: {
    type: Boolean,
    default: false
  },
  placeholder: {
    type: String,
    default: '请选择'
  },
  /* 列配置 */
  columnConfig: {
    type: Array,
    default(rawProps) {
      return []
    }
  },
  options: {
    type: Array,
    default(rawProps) {
      return []
    }
  },
  valueName: {
    type: String,
    default: 'id'
  },
  keyName: {
    type: String,
    default: ''
  }
})

const selectValue = ref('')

// 实际 keyName
// 优先使用 keyName，keyName 为空时，使用 valueName
const keyNameCom = computed(() => {
  return props.keyName !== '' ? props.keyName : props.valueName
})

// 监听父组件传递过来的值变化
watch(
  () => props.modelValue,
  (newValue, oldValue) => {
    selectValue.value = newValue
  },
  {
    immediate: true
  }
)

// 监听输入框值变化
watch(
  selectValue,
  (val, oldVal) => {
    console.log('watch selectValue change, val, oldVal:', val, oldVal)
    if (val != oldVal) {
      emit('update:modelValue', selectValue.value)
    }
  }
)

const handleChange = val => {
  emit('change', val)
}


// ref
const inputRef = ref(null)

// 渲染用的options
const showOptions = ref(props.options)
watch(
  () => props.options,
  newValue => {
    showOptions.value = newValue
    selectValue.value = ''
  },
)
// 筛选
// const filterMethod = (queryString) => {
//   showOptions.value =  props.options.filter(item => {
//     const JSONStr = JSON.stringify(item)
//     return JSONStr.includes(queryString) ? true : false
//   })
// }
const filterMethod = queryString => {
  showOptions.value = props.options.filter(item => (JSON.stringify(item).includes(queryString) ? true : false))
}

// 得到焦点
const getFocus = () => {
  nextTick(() => {
    inputRef.value.focus()
  })
}

defineExpose({
  getFocus
})
</script>

<style lang="scss" scoped></style>
<style scoped lang="scss">
.select-ul {
  padding-right: 0;
  list-style: none;
  display: flex;
  justify-content: space-between;
  text-align: center;
  padding-inline-start: 0;
  padding: 0 20px;
  > li {
    display: inline-block;
    width: 100px;
    // margin: 6px;
    overflow: hidden;
    text-overflow: ellipsis;
  }
}
li {
  // border: 1px solid red;
}
.select-ul-data {
  li {
    // color: #000;
  }
}
.el-select-dropdown__item {
  padding: 0;
}
</style>
