<template>
  <div class="channel-edit">
    <!-- 我的频道 -->
    <van-cell :border="false">
      <div slot="title" class="title-text">我的频道</div>
      <van-button
        class="edit-btn"
        type="danger"
        plain
        round
        size="mini"
        @click="isEdit = !isEdit"
      >
        {{ isEdit ? "完成" : "编辑" }}
      </van-button>
    </van-cell>

    <van-grid class="my-grid" :gutter="10">
      <van-grid-item
        class="grid-item"
        v-for="(myChannel, index) in myChannels"
        :key="index"
        :active="active"
        @click="onMyChannelClick(myChannel, index)"
      >
        <!--
          v-bind:class 语法
          一个对象，对象中的 key 表示要作用的 CSS 类名
                    对象中的 value 要计算出布尔值
                      true，则作用该类名
                      false，不作用类名
         -->
        <span
          class="text"
          slot="text"
          :class="{ active: index === active }"
        >
          {{ myChannel.name }}
        </span>
        <van-icon
          slot="icon"
          name="clear"
          v-show="isEdit && !fixedChannels.includes(myChannel.id)"
        >
        </van-icon>
      </van-grid-item>
    </van-grid>

    <!-- 频道推荐 -->
    <van-cell :border="false">
      <div slot="title" class="title-text">频道推荐</div>
    </van-cell>

    <van-grid class="recommend-grid" :gutter="10">
      <van-grid-item
        class="grid-item"
        v-for="(recommendChannel, index) in recommendChannels"
        :key="index"
        icon="plus"
        :text="recommendChannel.name"
        @click="onAddChannel(recommendChannel)"
      />
    </van-grid>
  </div>
</template>

<script>
import {
  getAllChannels,
  addUserChannel,
  deleteUserChannel
} from '@/api/channel'
import { mapState } from 'vuex'
import { setItem } from '@/utils/storage'
export default {
  data () {
    return {
      allChannels: [],
      myChannels: this.my_channels,
      isEdit: false,
      fixedChannels: [0] // 存的id
    }
  },
  props: {
    my_channels: {
      type: Array,
      required: true
    },
    active: {
      type: Number,
      required: true
    }
  },
  computed: {
    recommendChannels () {
      //   const channels = []
      //   this.allChannels.forEach(channel => {
      //     const isHasMyChannle = this.myChannels.find(myChannel => {
      //       // console.log('@myChannel.id', myChannel.id, '@channel.id', channel.id)
      //       return myChannel.name === channel.name
      //     })
      //     if (!isHasMyChannle) {
      //       channels.push(channel)
      //     }
      //   })
      //   return channels
      console.log('computed')
      return this.allChannels.filter((channel) => {
        return !this.myChannels.find((myChannel) => {
          return channel.name === myChannel.name
        })
      })
    },
    ...mapState(['user'])
  },
  created () {
    this.loadAllChannels()
  },
  methods: {
    async loadAllChannels () {
      try {
        const { data } = await getAllChannels()
        this.allChannels = data.data.channels
      } catch (error) {
        console.log(error)
      }
    },
    async onAddChannel (channel) {
      this.myChannels.push(channel)
      if (this.user) {
        try {
          // 已登录，把数据请求接口放到线上
          addUserChannel({
            id: channel.id,
            seq: this.myChannels.length
          })
        } catch (error) {
          this.$toast('添加用户频道失败', error)
        }
      } else {
        // 未登录，把数据存储到本地
        setItem('TOUTIAO_CHANNELS', this.myChannels)
      }
    },
    onMyChannelClick (myChannel, index) {
      if (this.isEdit) {
        // 1. 如果是固定频道，则不删除
        if (this.fixedChannels.includes(myChannel.id)) {
          return
        }
        // 2. 删除频道项

        this.myChannels.splice(index, 1)
        this.deleteChannel(myChannel)
        // 3. 如果删除的激活频道之前的频道，则更新激活的频道项
        // 参数1：要删除的元素的开始索引（包括）
        // 参数2：删除的个数，如果不指定，则从参数1开始一直删除到最后
        if (index <= this.active) {
          // 让激活频道的索引 - 1
          this.$emit('update-active', this.active - 1, true)
        }
      } else {
        // 非编辑状态，执行切换频道
        this.$emit('update-active', index, false)
      }
    },
    async deleteChannel (myChannel) {
      if (this.user) {
        // 已登录，则将数据更新到线上
        try {
          await deleteUserChannel(myChannel.id)
        } catch (error) {
          this.$toast('删除频道失败', error)
        }
      } else {
        // 未登录，将数据更新到本地
        setItem('TOUTIAO_CHANNELS', this.myChannels)
      }
    }
  }
}
</script>

<style scoped lang="less">
.channel-edit {
  padding: 85px 0;
  .title-text {
    font-size: 32px;
    color: #333333;
  }

  .edit-btn {
    width: 104px;
    height: 48px;
    font-size: 26px;
    color: #f85959;
    border: 1px solid #f85959;
  }
  /deep/ .grid-item {
    width: 160px;
    height: 86px;
    .van-grid-item__content {
      white-space: nowrap;
      background-color: #f4f5f6;
      .van-grid-item__text,
      .text {
        font-size: 28px;
        color: #222;
        margin-top: 0;
      }
      .active {
        color: red;
      }
      .van-grid-item__icon-wrapper {
        position: unset;
      }
    }
  }
  /deep/ .my-grid {
    .grid-item {
      .van-icon-clear {
        position: absolute;
        right: -10px;
        top: -10px;
        font-size: 30px;
        color: #cacaca;
        z-index: 2;
      }
    }
  }
  /deep/ .recommend-grid {
    .grid-item {
      .van-grid-item__content {
        flex-direction: row;
        .van-icon-plus {
          font-size: 28px;
          margin-right: 6px;
        }
      }
    }
  }
}
</style>
