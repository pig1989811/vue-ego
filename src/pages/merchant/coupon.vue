<template>
  <div>
    <header-top title="企业充值"></header-top>
    <main class='scroll-content-2'>
      <yd-cell-group title="赠送对象">
        <yd-cell-item>
          <span slot="left">赠送数目：</span>
          <yd-input slot="right" v-model="money" placeholder="输入数额"></yd-input>
        </yd-cell-item>
        <yd-cell-item>
          <span slot="left">赠送会员：</span>
          <input type="tel" slot="right" v-model="mobile" placeholder="请输入会员手机号" @input="findMember">
        </yd-cell-item>
        <yd-cell-item v-if="mobileName">
          <span slot="left">会员名称：</span>
          <yd-input slot="right" readonly v-model="mobileName" :show-clear-icon="false"></yd-input>
        </yd-cell-item>
        <yd-cell-item type="radio">
          <span slot="left">兑换券</span>
          <input slot="right" type="radio" value="0" v-model="tradeType" />
        </yd-cell-item>
        <yd-cell-item type="radio">
          <span slot="left">积分</span>
          <input slot="right" type="radio" value="1" v-model="tradeType" />
        </yd-cell-item>
      </yd-cell-group>
      <yd-cell-group v-if="payMoney">
        <yd-cell-item>
          <span slot="left">应付金额</span>
          <span slot="right" style="font-size:0.3rem;color:#ff5350;">{{payMoney}}</span>
        </yd-cell-item>
      </yd-cell-group>
      <yd-cell-group title="支付方式" v-show="money>0">
        <yd-cell-item type="radio">
          <span slot="icon" class="iconfont-large self-zhifubao" style="color:#00a0ea;"></span>
          <span slot="left">支付宝</span>
          <input slot="right" type="radio" value="1" v-model="payType" />
        </yd-cell-item>
      </yd-cell-group>
      <div class="btn-container flex just-around" style="padding:0 .2rem;">
        <yd-button type="warning" @click.native="goCouponHistory" style="font-size:15px;"> 更 多 记 录
          <span class="iconfont self-right"></span>
        </yd-button>
        <yd-button :type="valid?'primary':'disabled'" @click.native="save" style="font-size:15px;"> 确 认 赠 送
          <span class="iconfont self-dui"></span>
        </yd-button>
      </div>
    </main>
  </div>
</template>
<script>
import { mapState } from "vuex";
import HeaderTop from 'components/header/index'
import { addMemberVonchersHistory} from '../../api/index';
import { findMemberByMobile, payMixin } from "components/common/mixin";
export default {
  name: 'Coupon',
  data() {
    return {
      money: "",
      mobile: "",
      mobileName: "",
      payType: "",
      tradeType:"0", //0:兑换券 1:积分
      pays: {}
    }
  },
  components: { HeaderTop },
  computed: {
    ...mapState(["account", "member","fhbMoney"]),
    valid() {
      return (
        /^(([1-9]\d*)|([0-9]+\.[0-9]{1,2}))$/.test(this.money) &&
        /^1[3,4,5,7,8,9]\d{9}$/.test(this.mobile) &&
        !!this.payType
      );
    },
    payMoney() {
      return this.tradeType == "0"
        ? (+this.money*0.01).toFixed(2)
        : (+this.money * 0.1).toFixed(2);
    }
  },
  created() {
    this.$store.dispatch("getFHB");
  },
  activated() {

  },
  mixins: [findMemberByMobile, payMixin],
  methods: {
    save() {
      if (this.account == this.mobile) {
        this.$dialog.alert({
          mes: "赠送会员不能是自己"
        });
        return;
      }
      let vm = this;
      this.$dialog.loading.open("赠送中...");
      mui.ajax({
        url: addMemberVonchersHistory,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          amount: this.money,
          mobile: this.mobile,
          payType: this.payType,
          tradeType:this.tradeType,
          account: this.account,
          token: md5(`gjfengaddMemberVonchersHistory${this.account}${this.mobile}`)
        },
        success(res) {
          vm.$dialog.loading.close();
          //授信额度|天天宝直接录入
          if (vm.payType == "4" ) {
            vm.$dialog.toast({
              mes: res.msg
            });
            vm.$store.dispatch("getInfo");
          } else if (vm.payType == "6") {
            vm.$dialog.toast({
              mes: res.msg
            });
            vm.$store.dispatch("getFHB");
          } else if (vm.payType == "1") {
            //支付宝
            vm.checkService(vm.pays["alipay"], function() {
              plus.payment.request(
                vm.pays["alipay"],
                res.result.alyString,
                function(result) {
                  vm.$dialog.alert({
                    mes: "支付成功！"
                  });
                },
                function(e) {
                  vm.$dialog.alert({
                    mes: "支付失败:" + e.message
                  });
                }
              );
            });
          } else {
            //微信支付
          }
        },
        error() {
          vm.$dialog.loading.close();
          vm.$dialog.alert({
            mes: "端口异常，请稍后重试"
          });
        }
      });
    },
    goCouponHistory() {
      this.$router.push({ name: "CouponHistory" });
    }
  }
}
</script>
