<template>
  <div>
    <el-form ref="formRef" :model="formData" :rules="rules" size="default" label-width="100px">
      <el-form-item label="专业方向" prop="subject">
        <el-checkbox-group v-model="formData.subject" size="default">
          <el-checkbox-button v-for="(item, index) in subjectOptions" :key="index" :label="item.value"
            :disabled="item.disabled">{{item.label}}</el-checkbox-button>
        </el-checkbox-group>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm">确认</el-button>
        <el-button @click="resetForm">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script setup>
import {
  ElMessage
}
from 'element-plus'
const formRef = ref()
const data = reactive({
  formData: {
    subject: [],
  },
  rules: {
    subject: [{
      required: true,
      type: 'array',
      message: '请至少选择一个subject',
      trigger: 'change'
    }],
  }
})
const {
  formData,
  rules
} = toRefs(data)
const subjectOptions = ref([{
  "label": "Java",
  "value": 1
}, {
  "label": "Python",
  "value": 2
}, {
  "label": "C++",
  "value": 3
}])
/**
 * @name: 表单提交
 * @description: 表单提交方法
 * @return {*}
 */
// choose_subject.vue
function submitForm() {
  formRef.value.validate((valid) => {
    if (valid) {
      // 表单验证成功后，进行页面跳转
      window.location.href = 'https://www.bilibili.com'; // 将这里的URL替换为你想要跳转的外部网址
    } else {
      // 如果表单验证失败，可以在这里添加一些提示信息
      ElMessage.error('表单验证失败，请检查填写的信息');
    }
  });
}
/**
 * @name: 表单重置
 * @description: 表单重置方法
 * @return {*}
 */
function resetForm() {
  formRef.value.resetFields()
}


</script>
<style>
</style>
