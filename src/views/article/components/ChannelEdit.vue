<script setup>
import { ref } from 'vue'
import { artEditChannelService, artAddChannelService } from '@/api/article.js'
import { ElMessage } from 'element-plus'

const dialogVisible = ref(false)
const open = (row) => {
  console.log(row)
  dialogVisible.value = true
  formModel.value = { ...row }
}

const formRef = ref()
const formModel = ref({
  cate_name: '',
  cate_alias: ''
})
const rules = {
  cate_name: [
    { required: true, message: '请输入分类名称', trigger: 'blur' },
    {
      pattern: /^\S{1,10}$/,
      message: '分类名必须是1-10位非空字符',
      trigger: 'blur'
    }
  ],
  cate_alias: [
    { required: true, message: '请输入分类别名', trigger: 'blur' },
    {
      pattern: /^[a-zA-Z0-9]{1,15}$/,
      message: '分类名必须是1-15位字母或数字',
      trigger: 'blur'
    }
  ]
}
const emit = defineEmits(['success'])
const onSubmit = async () => {
  await formRef.value.validate()
  formModel.value.id
    ? await artEditChannelService(formModel.value)
    : await artAddChannelService(formModel.value)
  ElMessage({
    type: 'success',
    message: formModel.value.id ? '编辑成功' : '添加成功'
  })
  dialogVisible.value = false
  emit('success')
}

defineExpose({
  open
})
</script>

<template>
  <div>
    <el-dialog
      v-model="dialogVisible"
      :title="formModel.id ? '编辑分类' : '添加分类'"
      width="30%"
    >
      <el-form :model="formModel" :rules="rules" ref="formRef">
        <el-form-item label="分类名称" prop="cate_name">
          <el-input
            placeholder="请输入分类名称"
            v-model="formModel.cate_name"
          ></el-input>
        </el-form-item>
        <el-form-item label="分类别名" prop="cate_alias">
          <el-input
            placeholder="请输入分类别名"
            v-model="formModel.cate_alias"
          ></el-input>
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="dialogVisible = false">取消</el-button>
          <el-button type="primary" @click="onSubmit"> 确认 </el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>
