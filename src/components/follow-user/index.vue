<template>
  <!-- 关注按钮 -->

  <van-button
    v-if="isFollowed"
    class="follow-btn"
    round
    size="small"
    :loading="followLoading"
    @click="onfollow()"
    >已关注</van-button
  >
  <van-button
    v-else
    class="follow-btn"
    type="info"
    color="#3296fa"
    round
    size="small"
    icon="plus"
    :loading="followLoading"
    @click="onfollow()"
    >关注</van-button
  >
</template>

<script>
import { addFollow, deleteFollow } from '@/api/user'
export default {
  name: 'FollowUser',
  data () {
    return {
      followLoading: false
    }
  },
  // 自定义 v-model 的数据名称
  model: {
    prop: 'isFollowed', // 默认是 value
    event: 'update-is_followed' // 默认是 input
  },
  props: {
    isFollowed: {
      type: Boolean,
      required: true
    },
    // value: {
    //   type: Boolean,
    //   required: true
    // },
    userId: {
      type: [Number, String, Object],
      required: true
    }
  },
  methods: {
    async onfollow () {
      this.followLoading = true
      try {
        // 取消关注
        if (this.isFollowed) {
          await deleteFollow(this.userId)
        } else {
          await addFollow(this.userId)
        }
        this.$emit('update-is_followed', !this.isFollowed)
      } catch (error) {
        let message = '关注操作失败'
        if (error.response && error.response.status === 400) {
          message = '你不能关注你自己'
        }
        this.$toast(message)
      }
      this.followLoading = false
    }
  }
}
</script>

<style>
</style>
