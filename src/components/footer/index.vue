<template>
  <yd-tabbar active-color="#ff5350" :fixed="true">
    <yd-tabbar-item title="首页" link="/online/index" :active="$route.path.indexOf('/online/index') !== -1">
      <span :class="['iconfont-large' ,$route.path.indexOf('/online/index') !== -1?'self-home':'self-home1']" slot="icon" style="font-size:23px;"></span>
    </yd-tabbar-item>
    <yd-tabbar-item title="订单" :active="$route.path.indexOf('/order/index') !== -1" type="a" @click.native="goOrder">
      <span class="iconfont-large self-dingdan2" slot="icon" style="font-size:21px;"></span>
    </yd-tabbar-item>
    <yd-tabbar-item title="采购专区" :active="$route.path.indexOf('/merchant/index') !== -1" type="a" @click.native="goMerchant">
      <span class="iconfont-large self-caigou" slot="icon"></span>
    </yd-tabbar-item>
    <yd-tabbar-item title="购物车" link="/online/shoppingcart" :active="$route.path.indexOf('/online/shoppingcart') !== -1">
      <span class="iconfont-large self-cart" slot="icon"></span>
    </yd-tabbar-item>
    <yd-tabbar-item title="我的" link="/me/index" :active="$route.path.indexOf('/me/index') !== -1">
      <span :class="['iconfont-large', $route.path.indexOf('/me/index') !== -1?'self-me':'self-me2']" slot="icon" style="font-size:21px;"></span>
    </yd-tabbar-item>
  </yd-tabbar>
</template>
<script>
import { mapState } from "vuex";
export default {
  name: "FooterBar",
  computed: { ...mapState(["member"]) },
  methods: {
    goOrder() {
      this.$router.push({
        name: "MyOrder",
        query: { id: 0 },
        params: { update: true }
      });
    },
    goMerchant() {
      if (!this.member) {
        this.$dialog.alert({
          mes: "请先登录！",
          callback: () => {
            this.$router.push({ name: "Login" });
          }
        });
        return;
      }
      if (this.member.merchantType == "0") {
        this.$router.push({ name: "Upgrade" });
      } else {
        this.$router.push({ name: "MerchantIndex", params: { update: true } });
      }
    }
  }
};
</script>
<style lang='css' scoped>
.yd-tabbar {
  position: fixed !important;
  bottom: 0;
  height: 1rem;
}
</style>