<template>
  <!-- 此组件是MV的播放,并不能一开始就播放视频 -->
  <div>
    <div class="video-content">
      <div class="video-info">
        <!-- mv视频 -->
        <video-contet :video-url="videoUrl"></video-contet>
        <!-- 视频信息 -->
        <video-desc :mv-data="mvData" :like-count="likeCount"></video-desc>
      </div>
      <div class="recom-video">
        <!-- 相关视频 -->
        <video-recom
          :recom-video="recomVideo"
          @playVideo="playVideo"
        ></video-recom>
      </div>
    </div>
    <video-comment
      :mv-comment="mvComment"
      :mv-hot-comment="mvHotComment"
      :mvId="Number(mvId)"
      :mv-total='total'
    ></video-comment>
    <!-- 分页 -->
    <div class="pagination">
      <el-pagination
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-size="pageSize"
        layout="prev, pager, next"
        :total="total"
        background
      >
      </el-pagination>
    </div>
  </div>
</template>
<script>
import VideoContet from "@/components/VideoDetail/VideoContet";
import VideoDesc from "@/components/VideoDetail/VideoDesc";
import VideoRecom from "@/components/VideoDetail/VideoRecom";
import VideoComment from "@/components/VideoDetail/VideoComment";
import {
  getMvPlay,
  getMvData,
  getRecomMv,
  getMvLiked,
  getMvComment,
  getVideoUrl,
  getVideoDetails,
  getVideoComment
} from "../../network/mvdetails";
export default {
  data() {
    return {
      mvId: 0,
      videoUrl: "",
      mvData: {},
      recomVideo: [],
      likeCount: {},
      mvComment: [],
      mvHotComment: [],
      limit: 50,
      offset: 0,
      currentPage: 1,
      pageSize: 50,
      total: 0
    };
  },
  methods: {
    //获取mv播放地址
    async getMvPlay(id) {
      const res = await getMvPlay(id);
      this.videoUrl = res.data.url;
    },
    //获取mv介绍
    async getMvData(id) {
      const res = await getMvData(id);
      this.mvData = res.data;
    },
    //推荐相关mv
    async getRecomMv(id) {
      const res = await getRecomMv(id);
      this.recomVideo = res.data;
    },
    //获取点赞数/转发数
    async getMvLiked(id) {
      const res = await getMvLiked(id);
      this.likeCount = res;
    },
    //获取mv评论
    async getMvComment(id, limit, offset) {
      const res = await getMvComment(id, limit, offset);
      this.mvComment = res.comments;
      this.mvHotComment = res.hotComments;
      this.total = res.total;
    },
    //获取推荐视频的播放地址
    async getVideoPlay(id) {
      const res = await getVideoUrl(id);
      this.videoUrl = res.urls[0].url;
    },
    //获取推荐视频介绍
    async getVideoDetails(id) {
      const res = await getVideoDetails(id);
      this.mvData = res.data;
    },
    //获取推荐视频评论
    async getVideoComment(id) {
      const res = await getVideoComment(id);
      this.mvComment = res.comments;
      this.mvHotComment = res.hotComments;
    },
    //播放推荐视频更新数据
    playVideo(vid) {
      this.getVideoPlay(vid);
      this.getVideoDetails(vid);
      this.getRecomMv(vid);
      this.getMvLiked(vid);
      this.getVideoComment(vid);
    },
    //分页
    handleCurrentChange(newPage) {
      this.currentPage = newPage;
      this.offset = (this.currentPage - 1) * this.limit;
      this.getMvComment(this.mvId, this.limit, this.offset);
    }
  },
  components: {
    VideoContet,
    VideoDesc,
    VideoRecom,
    VideoComment
  },
  mounted() {
    this.mvId = this.$route.query.id;
    this.getMvPlay(this.mvId);
    this.getMvData(this.mvId);
    this.getRecomMv(this.mvId);
    this.getMvLiked(this.mvId);
    this.getMvComment(this.mvId, this.limit, this.offset);
  }
};
</script>
<style lang="less" scoped>
.video-content {
  width: 1200px;
  display: flex;
  justify-content: space-around;
  .pagination {
    width: 554px;
    height: 32px;
    margin: 50px auto;
  }
}
</style>