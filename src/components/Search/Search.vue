<template>
  <div class="search">
    <el-input
      :placeholder="defaultKeyWords"
      prefix-icon="el-icon-search"
      v-model="keywords"
      @change="searchContent"
      @focus="focusEvent"
      @blur.prevent="blurEvent"
    >
    </el-input>
    <hot-search
      :hot-search="hotSearch"
      v-show="isShowHotSearch"
      @searchHot="searchHot"
      ref="hotRef"
    ></hot-search>
  </div>
</template>
<script>
import HotSearch from "@/components/Search/HotSearch";
import {
  getDefaultKeyWords,
  getHotSearch,
  getSearchCotent
} from "../../network/search";

export default {
  data() {
    return {
      keywords: "",
      defaultKeyWords: "",
      hotSearch: [],
      isShowHotSearch: false,
      searchList: [],
      searchTotal: 0
      // hotSearchKeywords:[]
    };
  },
  methods: {
    //获取默认搜索关键字
    async getDefaultKeyWords() {
      const res = await getDefaultKeyWords();
      this.defaultKeyWords = res.data.showKeyword;
    },
    //获取热搜榜
    async getHotSearch() {
      const res = await getHotSearch();
      this.hotSearch = res.data;
    },
    searchContent() {
      this.$router.push({
        path: "/search",
        query: { keywords: this.keywords }
      });
      this.$bus.$emit("changeSearch", this.keywords);
      this.isShowHotSearch = false;
    },
    focusEvent() {
      this.isShowHotSearch = true;
      // this.searchHot(this.keywords)
    },
    blurEvent() {
      setTimeout(() => {
        this.isShowHotSearch = false;
      }, 200);
    },
    searchHot(keywords) {
      this.keywords = keywords;
      this.$router.push({
        path: "/search",
        query: { keywords: keywords }
      });
      this.$bus.$emit("changeSearch", keywords);
      this.isShowHotSearch = false;
    }
  },
  created() {
    this.getDefaultKeyWords();
    this.getHotSearch();
  },
  components: {
    HotSearch
  },
  mounted() {}
};
</script>
<style lang="less" scoped>
.el-input {
  width: 200px;
  margin-top: 10px;
  padding: 5px 5px;
}
/deep/ .el-input__inner {
  border-radius: 20px;
  background-color: rgba(255, 77, 77, 0.3);
}
  /deep/ input::-webkit-input-placeholder {
    color: white;
  }
  /deep/ input::-moz-input-placeholder {
    color: white;
  }
  /deep/ input::-ms-input-placeholder {
    color: white;
  }
</style>