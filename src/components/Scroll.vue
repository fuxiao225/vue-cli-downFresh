<template>
  <div>
    <!-- 2. 给想要加载的数据列表外层添加该标签和方法 -->
    <Scroll :flag="finish" :on-refresh="onRefresh" :on-infinite="onInfinite">
      <div>
        <div class="list" v-for="(item,index) in currentlist" :key="index">{{item.title}}</div>
      </div>
    </Scroll>
  </div>
</template>
<script>
import Scroll from "@/components/PullToRefresh.vue"; //  1.在此引入封装好的刷新和加载的插件
export default {
  data() {
    return {
      page: 1,
      pages: 5,
      endId: "", //记录上拉加载时应该从哪一条数据开始取
      pageCount: "", //每次请求的条数
      currentlist: [
        {
          name: "哈哈哈"
        },
        {
          name: "哈哈哈"
        },
        {
          name: "哈哈哈"
        }
      ], //当前显示的列表
      finish: false
    };
  },
  created() {
    // console.log("qwe");
    this.onRefresh();
  },
  methods: {
    /**
     * 下拉刷新方法
     */
    onRefresh: function(done) {
      //   3. 在刷新方法内部进行自己的逻辑处理 此处调用了后台接口
      this.onRefreshPort(done);
      this.finish = false;
    },

    /** 加载数据成功的回调
     * 下拉刷新接口
     */
    onRefreshPort(done) {
      let url = "course/api/mydiary";
      let that = this;
      let pages = that.pages;
      let params = {
        phone: "18837181108",
        page: 1,
        pages: pages
      };
      console.log(params);
      this.$http.post(url, params, { emulateJSON: true }).then(suc => {
        console.log(suc);
        if (suc.status == 200) {
          this.currentlist = suc.body.data;
          done();
          this.page = 2;
          //   console.log(suc.body);
        }
      });
    },

    /**
     * 上拉加载方法
     */
    onInfinite: function(done) {
      this.onInfinitePort(done);
    },
    /**
     * 上拉加载接口
     */
    onInfinitePort(done) {
      let url = "course/api/mydiary";
      let that = this;
      let page = that.page;
      let pages = that.pages;
      let params = {
        phone: "18837181108",
        page: page,
        pages: pages
      };
      console.log(params);
      this.$http.post(url, params, { emulateJSON: true }).then(suc => {
        console.log(suc);
        if (suc.status == 200) {
          if (suc.body.data.length > 0) {
            this.currentlist = this.currentlist.concat(suc.body.data);
            done();
            this.page++;
          } else {
            done();
            this.finish=true
          }
          console.log(this.page);
          //   console.log(suc.body);
        }
      });
    }
  },
  components: {
    Scroll
  }
};
</script>
<style>
.list {
  padding: 100px 0;
}
</style>