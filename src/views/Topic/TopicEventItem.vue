<template>
  <div class="eventitem">
    <div class="user-info">
      <div
        class="img"
        v-if="
          eventItem.user.avatarUrl &&
            Object.keys(eventItem.user.avatarUrl).length !== 0
        "
      >
        <img
          :src="eventItem.user.avatarUrl"
          @click="getUser(eventItem.user.userId)"
        />
      </div>
      <div class="name">
        <div class="nickname" @click="getUser(eventItem.user.userId)">
          <span class="nick">{{ eventItem.user.nickname }}</span>
          <span v-if="eventItem.type === 18">分享单曲</span>
          <span v-if="eventItem.type === 19">分享专辑</span>
          <span v-if="eventItem.type === 17 || eventItem.type === 28"
            >分享电台节目</span
          >
          <span v-if="eventItem.type === 22">转发</span>
          <span v-if="eventItem.type === 33">发布视频</span>
          <span v-if="eventItem.type === 35 || eventItem.type === 13"
            >分享歌单</span
          >
          <span v-if="eventItem.type === 24">分享专栏文章</span>
          <span v-if="eventItem.type === 41 || eventItem.type === 21"
            >分享视频</span
          >
        </div>
        <div class="time" @click="getDynamicComment(eventItem.info.threadId)">
          {{ eventTime }}
        </div>
      </div>
    </div>
    <div class="event-desc">
      <div class="title">{{ eventMsg }}</div>
      <div
        class="songs"
        v-if="eventDesc.song && Object.keys(eventDesc.song).length !== 0"
        @click="getMusicPlay(eventDesc.song.id)"
      >
        <div class="img">
          <img :src="eventDesc.song.album.picUrl" />
          <div class="icon"><i class="iconfont icon-bofang1"></i></div>
        </div>
        <div class="info">
          <div class="songname">{{ eventDesc.song.name }}</div>
          <div class="artist">{{ eventDesc.song.artists[0].name }}</div>
        </div>
      </div>
      <div
        class="songs"
        v-if="
          eventDesc.playlist && Object.keys(eventDesc.playlist).length !== 0
        "
        @click="getPlayList(eventDesc.playlist.id)"
      >
        <div class="img">
          <img :src="eventDesc.playlist.coverImgUrl" />
        </div>
        <div class="info">
          <div class="songname">
            <span style="color:red">歌单</span> {{ eventDesc.playlist.name }}
          </div>
          <div class="artist">{{ eventDesc.playlist.creator.nickname }}</div>
        </div>
      </div>
      <forword-dynamic
        v-if="hasFoworder && Object.keys(hasFoworder).length !== 0"
        :has-foworder="hasFoworder"
      ></forword-dynamic>
    </div>
    <div class="dynamicImg">
      <div
        class="origin-img"
        v-for="(item, index) in eventItem.pics"
        :key="index"
      >
        <img
          :src="item.originUrl"
          :class="{
            active: eventItem.pics.length === 1,
            active1: eventItem.pics.length === 2
          }"
        />
      </div>
    </div>
    <div class="opertion">
      <span
        class="iconfont icon-zan"
        @click="zanDynamic"
        :style="isLiked ? 'color:red' : ''"
        >({{ eventItem.info.likedCount }})</span
      >
      <span class="iconfont icon-forward1" @click="showDynamic"
        >({{ eventItem.info.shareCount }})</span
      >
      <span
        class="iconfont icon-huifu"
        @click="getDynamicComment(eventItem.info.threadId)"
        >({{ eventItem.info.commentCount }})</span
      >
    </div>
    <!-- 转发对话框 -->
    <el-dialog title="转发" :visible.sync="dialogVisible" width="30%">
      <textarea
        name="textarea"
        v-model="forwards"
        class="text"
        cols="55"
        rows="8"
      ></textarea>
      <el-button type="danger" round @click="forwardDynamic">转发</el-button>
    </el-dialog>
  </div>
</template>
<script>
import ForwordDynamic from "@/views/Topic/ForwordDynamic";
import { dateFormat } from "../../utils/utils";
import { userDynamicForward, likeDynamic } from "../../network/dynamic";
export default {
  name: "TopicEventItem",
  props: {
    eventItem: {
      type: Object,
      default() {
        return {};
      }
    }
  },
  data() {
    return {
      eventDesc: {},
      eventMsg: "",
      dialogVisible: false,
      forwards: "",
      uid: 0,
      evId: 0,
      zanQueryInfo: {
        type: 6,
        t: 1,
        threadId: ""
      },
      isLiked: false,
      hasFoworder: {}
    };
  },
  methods: {
    //播放歌曲
    getMusicPlay(id) {
      this.$bus.$emit("getMusic", id);
    },
    //获取用户信息
    getUser(id) {
      this.$router.push({ path: "/user", query: { id: id } });
    },
    //获取动态评论
    getDynamicComment(id) {
      this.$router.push({ path: "/dynamiccomment", query: { id: id } });
    },
    //转发动态
    async DynamicForward(uid, evId, forwards) {
      const res = await userDynamicForward(uid, evId, forwards);
      this.hasFoworder = res.event;
      // console.log(res);
    },
    //点赞动态
    async getLikeDynamic(params) {
      const res = await likeDynamic(params.type, params.t, params.threadId);
    },
    showDynamic() {
      this.dialogVisible = true;
    },
    //转发用户动态
    forwardDynamic() {
      this.uid = this.eventItem.user.userId;
      this.evId = this.eventItem.id;
      this.DynamicForward(this.uid, this.evId, this.forwards);
      this.dialogVisible = false;
    },
    //点赞动态
    zanDynamic() {
      this.zanQueryInfo.threadId = this.eventItem.info.threadId;
      if (!this.isLiked) {
        this.eventItem.info.likedCount += 1;
        this.isLiked = true;
        this.getLikeDynamic(this.zanQueryInfo);
      } else {
        if (this.eventItem.info.likedCount !== 0) {
          this.eventItem.info.likedCount -= 1;
          this.isLiked = false;
        }
      }
    },
    //展开歌单
    getPlayList(id) {
      this.$router.push({ path: "/songdetails", query: { id: id } });
    }
  },
  computed: {
    eventTime() {
      return dateFormat(this.eventItem.eventTime);
    }
  },
  created() {
    this.eventDesc = JSON.parse(this.eventItem.json);
    // console.log(this.eventDesc);
    this.eventMsg = this.eventDesc.msg;
  },
  components: {
    ForwordDynamic
  }
};
</script>
<style lang="less" scoped>
.eventitem {
  width: 1040px;
  margin-bottom: 30px;
  border-bottom: 1px solid whitesmoke;
  padding-bottom: 15px;
  .user-info {
    width: 1060px;
    height: 40px;
    margin-bottom: 10px;
    cursor: pointer;
    display: flex;
    flex: 1;
    .img {
      width: 40px;
      height: 40px;
      margin-right: 10px;
      img {
        width: 40px;
        height: 40px;
        border-radius: 50%;
      }
    }
    .name {
      height: 40px;
      .nickname {
        font-size: 13px;
        margin-bottom: 10px;
        .nick {
          color: rgb(177, 177, 248);
          margin-right: 5px;
        }
      }
      .time {
        color: gainsboro;
        font-size: 12px;
      }
    }
  }
  .event-desc {
    width: 1020px;
    margin-left: 40px;
    margin-bottom: 20px;
    .title {
      width: 950px;
      color: black;
      font-size: 16px;
    }
    .songs {
      width: 1000px;
      height: 56px;
      background-color: whitesmoke;
      padding: 8px;
      margin-top: 10px;
      border-radius: 5px;
      cursor: pointer;
      .img {
        width: 40px;
        height: 40px;
        margin-right: 10px;
        float: left;
        position: relative;
        img {
          width: 40px;
          height: 40px;
          border-radius: 5px;
        }
        .icon {
          width: 20px;
          height: 20px;
          border-radius: 50%;
          background-color: whitesmoke;
          position: absolute;
          left: 10px;
          top: 10px;
          i {
            color: red;
            margin-left: 5px;
            font-size: 12px;
          }
        }
      }
      .info {
        width: 300px;
        height: 40px;
        .songname {
          margin-bottom: 5px;
          font-size: 13px;
        }
        .artist {
          font-size: 12px;
          color: gray;
        }
      }
    }
  }
  .dynamicImg {
    width: 340px;
    margin-left: 40px;
    display: flex;
    flex: 1;
    flex-wrap: wrap;
    .origin-img {
      margin-right: 5px;
      .active {
        width: 200px;
        height: 300px;
        img {
          width: 100%;
          height: 300px;
        }
      }
      .active1 {
        width: 200px;
        height: 200px;
        img {
          width: 100%;
          height: 200px;
        }
      }
      img {
        width: 100px;
        height: 100px;
        border-radius: 10px;
      }
    }
  }
  .opertion {
    width: 1040px;
    height: 30px;
    line-height: 30px;
    display: flex;
    flex: 1;
    margin-top: 10px;
    margin-left: 850px;
    span {
      cursor: pointer;
      margin-right: 30px;
      font-size: 12px;
      color: gainsboro;
    }
  }
  .el-button {
    margin-left: 340px;
    margin-top: 20px;
  }
  .el-button--danger {
    background-color: red;
    border-color: red;
  }
}
</style>