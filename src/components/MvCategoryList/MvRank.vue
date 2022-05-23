<template>
  <div class="mvrank">
    <div class="filter">
      <div class="title" @click='getMoreRank'>
        <h3>MV排行榜</h3>
        <span class="el-icon-arrow-right"></span>
      </div>
      <div class="area">
        <div
          class="areaitem"
          v-for="(item, index) in area"
          :style="item === sellectArea ? 'color:red' : ''"
          :key="index"
          @click="changeArea(item)"
        >
          {{ item }}
        </div>
      </div>
    </div>
    <div class="rankitem">
      <div
        class="mvrankitem"
        v-for="(item, index) in mvRankList"
        :key="item.id"
      >
        <mv-rank-item :mv-rank-item="item" :number="index + 1"></mv-rank-item>
      </div>
    </div>
  </div>
</template>
<script>
import MvRankItem from "@/components/MvCategoryList/MvRankItem";
export default {
  props: {
    mvRankList: {
      type: Array,
      default: () => []
    },
    area: {
      type: Array,
      default: () => []
    },
    sellectArea: {
      type: String,
      default: ""
    }
  },
  methods: {
    changeArea(area) {
      this.$emit("changeArea", area);
    },
    getMoreRank(){
      this.$router.push('/mvrankdetail');
    }
  },
  components: {
    MvRankItem
  }
};
</script>
<style lang="less" scoped>
.mvrank {
  width: 1160px;
  height: 600px;
  .filter {
    width: 1160px;
    height: 20px;
    display: flex;
    justify-content: space-between;
    .title {
      color: black;
      cursor: pointer;
      h3 {
        float: left;
        margin-right: 5px;
      }
      span {
        font-weight: bolder;
        font-size: 20px;
      }
      margin-right: 760px;
    }
    .area {
      width: 230px;
      height: 20px;
      display: flex;
      flex: 1;
      align-items: center;
      .areaitem {
        width: 30px;
        height: 10px;
        margin-right: 10px;
        font-size: 12px;
        color: gainsboro;
      }
    }
  }
  .rankitem {
    width: 1160px;
    height: 300px;
    display: flex;
    flex: 1;
    flex-wrap: wrap;
    margin-top: 10px;
    .mvrankitem {
      width: 550px;
      height: 120px;
      margin-top: 10px;
    }
  }
}
</style>