<template>
  <div>
    <header-top title="我的钱包"></header-top>
    <main class='scroll-content'>
      <!-- 金额展示 -->
      <section class="money-container text-center">
       <p class="orange-color">{{fhbMoney}}</p>
       <p>可提现总额（元）</p>
      </section>
      <section class="money-container2 flex align-center text-center">
        <div class="money-item flex-1" @click="goPage('/merchant/recommendhistory')">
          <p class="orange-color">{{member.directMemberMoney}}</p>
          <p>业务奖励</p>
        </div>
        <div class="money-item flex-1">
          <p class="orange-color">{{member.memberVoucherMoney}}</p>
          <p>代金券</p>
        </div>
        <div class="money-item flex-1"  @click="goPage('/trade/interest?type=0')">
          <p class="orange-color">{{member.consumptionMoney}}</p>
          <p>累计消费额</p>
        </div>
      </section>
      <yd-grids-group :rows="3" v-if="member.type=='0'">
        <yd-grids-item v-for="(item,i) in menu0" :key="'i'+i" :link="item.link">
          <span slot="icon" :class="['iconfont-large',item.icon]" :style="{color:item.color}"></span>
          <span slot="text">{{item.text}}</span>
        </yd-grids-item>
      </yd-grids-group>
      <yd-grids-group :rows="3" v-if="member.type=='1'">
        <yd-grids-item link="/treasure/index">
          <span slot="icon" class="iconfont-large self-fenhongjilu" style="color:rgb(223,9,9);"></span>
          <span slot="text">天天宝</span>
        </yd-grids-item>
        <yd-grids-item link="/trade/cashhistory">
          <span slot="icon" class="iconfont-large self-lishijilu" style="color:rgb(11,140,99);"></span>
          <span slot="text">提取历史</span>
        </yd-grids-item>
        <yd-grids-item link="/trade/bankcard">
          <span slot="icon" class="iconfont-large self-yinxingqia" style="color:rgb(30,125,193);"></span>
          <span slot="text">银行卡</span>
        </yd-grids-item>
        <!-- <yd-grids-item v-if="+member.merchantType>0" link="/merchant/give">
          <span slot="icon" class="iconfont-large self-shenghuofuwu" style="color: #e7d489;"></span>
          <span slot="text">升级赠送</span>
        </yd-grids-item> -->
        <yd-grids-item v-if="+member.merchantType>0" link="/merchant/coupon">
          <span slot="icon" class="iconfont-large self-daijinquan" style="color: #e7d489;"></span>
          <span slot="text">赠送代金券</span>
        </yd-grids-item>
        <!-- <yd-grids-item link="/trade/interest?type=0">
          <span slot="icon" class="iconfont-large self-xiaofeijilu" style="color: #663355;"></span>
          <span slot="text">消费记录</span>
        </yd-grids-item> -->
      </yd-grids-group>
    </main>
  </div>
</template>
<script>
import { mapState } from "vuex";
import HeaderTop from "components/header/index";
import { countMemberInfo, myWallet } from "../../api/index";
import { mixin, getStore } from "components/common/mixin";
export default {
  name: "MyWallet",
  data() {
    return {
      info0: {},
      info1: {},
      info: {},
      menu0: [
        {
          icon: "self-fenhongluji",
          text: "天天宝",
          link: "/treasure/index",
          color: "#e7d489"
        },
        {
          icon: "self-lishijilu",
          text: "提取历史",
          link: "/trade/cashhistory",
          color: "#663355"
        },
        {
          icon: "self-yinxingqia",
          text: "银行卡",
          link: "/trade/bankcard",
          color: "#e7d489"
        }
      ]
    };
  },
  components: { HeaderTop},
  computed: {
    ...mapState(["member","fhbMoney"])
  },
  mixins: [mixin],
  created() {},
  activated() {
    if (getStore("account") && getStore("account").length > 0) {
      this.$store.commit("SET_ACCOUNT", getStore("account"));
      //this.getInfo();
      setTimeout(()=>{
        this.getWallet();
      },200)
    } else {
      this.$router.push("/me/login");
    }
    if (this.$store.state.positions[this.$route.path]) {
      document.querySelector("main").scrollTop = this.$store.state.positions[
        this.$route.path
      ];
    }
  },
  methods: {
    getWallet() {
      let vm = this;
      mui.ajax({
        url: myWallet,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          account: getStore("account"),
          token: md5(`gjfengmyWallet${getStore("account")}`)
        },
        success(res) {
          vm.info = res.result;
        }
      });
    },
    goPage(link){
      this.$router.push(link);
    }
  }
};
</script>
<style lang='less' scoped>
@import "../../style/mixin.less";
.money-container {
  .pd;
  p:first-child {
    font-size: 0.8rem;
    font-weight: bold;
  }
}

.money-container2 {
  .pd-v;
  background-color: @white;
  border-bottom: 1px solid #f7f5f5;
  .money-item {
    p:first-child {
      font-size: 0.28rem;
    }
    p:last-child {
      font-size: 0.22rem;
    }
  }
}
.wallet-tab {
  background-color: @white;
  margin-bottom: @pd;
  .tab-item {
    width: 50%;
    a {
      .pd-v;
      display: block;
      p:last-child {
        margin-top: 0.1rem;
        font-size: 16px;
        height: 16px;
      }
    }
    &.tab-item2 {
      width: 50%;
    }
  }
}

.operate-container {
  .pd;
  .mg-v;
  background-color: @white;
}
</style>