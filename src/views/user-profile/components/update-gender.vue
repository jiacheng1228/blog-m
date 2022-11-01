<template>
<div class="update-gend">
    <van-picker
  title="标题"
  show-toolbar
  :columns="columns"
  :default-index="value"
  @confirm="onConfirm"
  @cancel="$emit('close')"
  @change="onPickerChange"
/>
</div>
</template>

<script>
import { updateUserProfile } from '@/api/user'
export default {
  data () {
    return {
      columns: ['男', '女'],
      localGender: this.value
    }
  },
  props: {
    value: {
      type: Number,
      required: true
    }
  },
  methods: {
    async onConfirm () {
      this.$toast.loading({
        message: '保存中...',
        forbidClick: true, // 禁止背景点击
        duration: 0 // 持续展示
      })
      try {
        const localGender = this.localGender
        const { data } = await updateUserProfile({
          gender: localGender
        })
        console.log('@updataProfile', data)
        // 更新视图
        this.$emit('input', localGender)

        // 关闭弹层
        this.$emit('close')

        // 提示成功
        this.$toast.success('更新成功')
      } catch (error) {
        this.$toast.fail('更新失败')
      }
    },
    onPickerChange (picker, value, index) {
      this.localGender = index
    }
  }

}
</script>

<style>

</style>
