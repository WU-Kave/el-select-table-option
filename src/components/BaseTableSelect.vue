<template>
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
    <!-- 表头 -->
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

const selectValue = computed({
  get: () => props.modelValue,
  set: val => {
    emit('update:modelValue', val)
  }
})

// 实际 keyName
// 优先使用 keyName，keyName 为空时，使用 valueName
const keyNameCom = computed(() => {
  return props.keyName !== '' ? props.keyName : props.valueName
})

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
const filterMethod = queryString => {
  if (!queryString) {
    showOptions.value = props.options;
    return;
  }

  showOptions.value = props.options.filter(item => {
    // 针对每个要过滤的列进行判断
    return props.columnConfig.some(config => {
      const propValue = item[config.prop];
      // 将属性值转换为字符串并检查是否包含查询字符串
      return propValue && propValue.toString().includes(queryString);
    });
  });
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
.el-select-dropdown__item {
  padding: 0;
}
</style>
