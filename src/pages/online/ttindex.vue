<template>
  <div>
    <header-top title="天天易购"></header-top>
    <main class='scroll-content-2'>
      <section class="pd-list" v-for="(item,index) in pds" :key="index">
        <yd-cell-group>
          <yd-cell-item arrow type="label" @click.native="goProducts(item.id)">
            <span slot="icon" class="iconfont self-package" style="color:#f98e25;font-size:20px;"></span>
            <span slot="left">{{item.label}}</span>
            <span slot="right">更多</span>
          </yd-cell-item>
        </yd-cell-group>
        <ul class="flex just-between">
          <router-link :to="{path:'/online/product',query:{id:pd.id}}" v-for="pd in item.list" :key="pd.id" tag="li" class="pd-item">
            <div class="img-cover">
              <img :src="pd.imgUrl" :alt="pd.name">
            </div>
            <div class="title flex align-center">
              <h3>{{pd.name}}</h3>
            </div>
            <div class="price" v-if="pd.isCanUserCou=='1'">
              <span>￥{{pd.pointNicePrice}}</span>+
              <span>{{pd.price}}积分</span>
            </div>
            <div class="price" v-else-if="pd.isCanUserCou=='2'">
              <span>￥{{formatPrice(pd.pointNicePrice)}}</span>+
              <span>{{pd.price}}责任金额</span>
            </div>
            <div class="price" v-else-if="pd.isCanUserCou=='3'">
              <span>{{pd.price}}代金券</span>
            </div>
            <div class="price" v-else>
              <span>￥{{formatPrice(pd.price)}}</span>
            </div>
          </router-link>
        </ul>
      </section>
      <section class="vocher-container" @click="goPlatform(1)">
        <img :src="getLocalImg('jd.jpg')">
      </section>
    </main>
  </div>
</template>
<script>
import HeaderTop from "components/header/index";
import { onlineInH5 } from "../../api/index";
import { mixin, localImg } from "components/common/mixin";
export default {
  name: "TTIndex",
  data() {
    return {
      pds: []
    };
  },
  components: { HeaderTop },
  computed: {},
  created() {
    this.getInfo();
  },
  mixins: [mixin, localImg],
  activated() {
    if (this.$store.state.positions[this.$route.path]) {
      document.querySelector("main").scrollTop = this.$store.state.positions[
        this.$route.path
      ];
    }
  },
  methods: {
    getInfo() {
      let vm = this;
      this.$dialog.loading.open();
      mui.ajax({
        url: onlineInH5,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          token: md5(`gjfengonlineInH5`)
        },
        success(res) {
          vm.$dialog.loading.close();
          let _pds = [];
          Object.entries(res.result.indexProducts).forEach((value, index) => {
            var item = {};
            var desc = value[0].split(/[;|~]/);
            item.label = desc[0];
            item.icon = desc[1];
            item.id = desc[2];
            item.list = value[1];
            _pds.push(item);
          });
          vm.pds = _pds;
        },
        error(e) {
          vm.$dialog.loading.close();
          vm.$dialog.toast({
            mes: "网络异常，请稍后重试！"
          });
        }
      });
    },
    goProducts(id) {
      this.$router.push({
        name: "Products",
        params: { update: true },
        query: { type: 1, id }
      });
    },
    goPlatform(type) {
      switch (type) {
        case 1:
          this.$router.push({ name: "JDSelf" });
          break;
      }
    }
  }
};
</script>
<style lang='less' scoped>
@import "../../style/mixin.less";
.container {
  margin-bottom: @pd;
  background-color: @white;
  img {
    width: 100%;
  }
}

.pd-list {
  background-color: @white;
  .pd-item {
    width: 33%;
    padding: 2px;
    margin-bottom: @pd;
    border: 1px solid #f7f5f5;
    .img-cover {
      position: relative;
      width: 100%;
      overflow: hidden;
      padding: 50% 0;
      img {
        width: 100%;
        .hv-cen;
      }
    }
    .title {
      height: 40px;
      line-height: 20px;
      h3 {
        .multi-ellipsis(2);
        font-size: 0.26rem;
        font-weight: normal;
        word-wrap: break-word;
        word-break: break-all;
        color: #333;
      }
    }
    .price {
      margin-top: 0.1rem;
      font-size: 14px;
      span:first-of-type {
        color: @red;
      }
      span:nth-of-type(2) {
        font-size: 12px;
        color: @lightgray;
      }
    }
  }
}
.vocher-container {
  img {
    width: 100%;
  }
}
</style>