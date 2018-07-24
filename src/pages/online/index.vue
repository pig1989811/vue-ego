<template>
  <div>
    <header class="head-top flex align-center">
      <div class="left-icon">
        <img :src="getLocalImg('user.png')">
      </div>
      <div class="search-container2 flex-1" @click="navigate('Products',{id:1398,type:1})">
        <span class="iconfont self-search"></span>
        <input type="text" disabled placeholder="搜索自营商品">
      </div>
      <div class="right-icon text-center">
        <router-link to="/news/index?update=true">
          <span class="iconfont-large self-message"></span>
        </router-link>
      </div>
    </header>
    <ul class="nav-list" ref="swipeList">
      <li v-for="(item,index) in column" :key="index" @click="navigate2('Products',{id:item.id,type:1},index)" class="nav-item" :class="{'nav-active':curIndex == index}">
        <span>{{item.names}}</span>
      </li>
    </ul>
    <main class='scroll-content-1' style="top: 1.7rem;">
      <yd-pullrefresh ref="homepage" :callback="refresh" :show-init-tip="false">
        <section>
          <yd-slider :loop="false" :autoplay="2000">
            <yd-slider-item v-for="(item,index) in info.indexAds" :key="item.id">
              <router-link :to="{name:'Product',query:{id:item.address.substring(item.address.lastIndexOf('/')+1),tips:0}}" v-if="index==0">
                <img :src="item.photo" :alt="item.names">
              </router-link>
            </yd-slider-item>
            <!-- <yd-slider-item @click.native="goMerchant">
              <img :src="getLocalImg('tt_2.jpg')" alt="采购专区">
            </yd-slider-item> -->
          </yd-slider>
        </section>
        <!-- 平台跳转 -->
        <section class="platform-container">
          <div class="login-btn" @click="login" v-if="!member">登录/注册</div>
          <div class=" flex just-around">
            <!-- <div v-for="(item,index) in platform" :key="index" class="platform-item flex-1" @click="navigate(item.link,{id:item.id,type:item.type})">
            </div> -->
            <div class="platform-item flex-1" @click="navigate('Products',{id:1398,type:1})">
              <img :src="getLocalImg('navigation_01.jpg')">
                <p>自营商城</p>
            </div>
            <div class="platform-item flex-1" @click="navigate('Products',{id:1357,type:1})">
              <img :src="getLocalImg('navigation_02.jpg')">
                <p>积分商城</p>
            </div>
            <div class="platform-item flex-1" @click="navigate('Products',{id:1415,type:1})">
              <img :src="getLocalImg('navigation_03.jpg')">
                <p>兑换券商城</p>
            </div>
            <div class="platform-item flex-1" @click="navigate('Coupon')" v-if="member&&(+member.merchantType>0)">
              <img :src="getLocalImg('navigation_04.jpg')">
                <p>充值中心</p>
            </div>
            <div class="platform-item flex-1" @click="navigate('')">
              <img :src="getLocalImg('navigation_05.jpg')">
                <p>睿德教育</p>
            </div>
          </div>
        </section>
        <!-- 资讯 -->
        <div class="flex align-center news-box" v-show="info.newList&&info.newList.result.length">
          <!-- <div class="flex align-center news-box"> -->
          <div class="rolltip">
            <img :src="getLocalImg('news.png')" alt="天天资讯">
          </div>
          <yd-rollnotice autoplay="4000" class="flex-1">
            <yd-rollnotice-item v-for="(item,index) in info.newList&&info.newList.result" :key="index">
              <router-link :to="{name:'NewsDetail',query:{id:item.id}}" class="flex align-center" style="width:100%;">
                <span class="news-tag">{{item.source}}</span>
                <span class="news-title flex-1 fs-14">{{item.title}}</span>
              </router-link>
            </yd-rollnotice-item>
          </yd-rollnotice>
          <router-link :to="{name:'News',query:{update:'true'}}" style="color:#fe6637;">更多</router-link>
        </div>
        <!-- 广告栏 -->
        <section class="high-container">
          <img :src="getLocalImg('20180529_1.png')">
        </section>
        <!-- 栏目跳转 -->
        <section class="fresh-container">
          <ul class="clearfix">
          <li class="fresh-item fl" @click="navigate('')">
            <img :src="getLocalImg('20180718_1.png')" alt="">
          </li>
          <li class="fresh-item fl" @click="navigate('')">
            <img :src="getLocalImg('20180718_2.png')" alt="">
          </li>
          <li class="fresh-item fl" @click="navigate('')">
            <img :src="getLocalImg('20180718_3.png')" alt="">
          </li>
          <li class="fresh-item fl" @click="navigate('')">
            <img :src="getLocalImg('20180718_4.png')" alt="">
          </li>
        </ul>
        </section>
        <!-- 商品展示 -->
        <section class="pd-list clearfix">
            <router-link :class="['pd-item fl',index==0?'pd-item__large':'pd-item__middle']" v-for="(item,index) in info.indexProducts&&info.indexProducts[0].product" :key="index" :to="{path:'/online/product',query:{id:item.id}}">
              <div class="img-cover">
                <img :src="item.imgUrl">
              </div>
              <div class="pd-info">
                <div class="pd-new" v-if="index==0">每日新品</div>
                <div class="pd-name">{{item.name}}</div>
              <!-- <div class="pd-attr">{{item.name}}</div> -->
              <div class="pd-price"><span class="danger-color">￥{{item.price}}</span></div>
              </div>
            </router-link>
        </section>
      </yd-pullrefresh>
      <yd-popup v-model="showTips" position="center" width="90%">
        <div class="tips-content">
          <div v-html="tipsInfo.contents"></div>
          <span class="iconfont-large self-guanbi close" @click="showTips=false"></span>
        </div>
      </yd-popup>
    </main>
    <footer-bar></footer-bar>
  </div>
</template>
<script>
import { mapState } from "vuex";
import HeaderTop from "components/header/index";
import FooterBar from "components/footer/index";
import { mixin, getStore, setStore, localImg } from "components/common/mixin";
import {
  ygOnlineShopIndex,
  findAppUpgredeByType,
  newNotice,
  allColumn
} from "../../api/index";
export default {
  name: "Online",
  data() {
    return {
      oldBack: mui.back,
      info: {},
      loginAccount: false,
      column:[],
      curIndex :0,
      scrollLeft:0, //记录tab滚动值
      colors: ["#dc0719", "#da5906", "#ff3964"],
      type: "", //APP环境
      curVersion: "", //app版本
      showTips: false,
      tipsInfo: {}
    };
  },
  components: { HeaderTop, FooterBar },
  computed: { ...mapState(["account", "member"]) },
  mixins: [mixin, localImg],
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.plusReady();
    });
  },
  beforeRouteLeave(to, from, next) {
    this.scrollLeft = this.$refs.swipeList.scrollLeft;
    mui.back = this.oldBack;
    next();
  },
  created() {
    this.init();
    this.getInfo();
    this.getTips();
    this.getColumn();
  },
  mounted() {
    document.addEventListener("plusready", this.getVersion, false);
  },
  activated() {
    if (getStore("account") && getStore("account").length > 0) {
      this.loginAccount = true;
      this.$store.commit("SET_ACCOUNT", getStore("account"));
      if (!this.member) {
        this.$store.dispatch("getInfo");
      }
    } else {
      this.loginAccount = false;
    }
    if (this.$store.state.positions[this.$route.path]) {
      document.querySelector("main").scrollTop = this.$store.state.positions[
        this.$route.path
      ];
    }
    this.$nextTick(()=>{
      this.$refs.swipeList.scrollLeft=this.scrollLeft;
    })
  },
  methods: {
    init() {
      let u = navigator.userAgent;
      let isAndroid = u.indexOf("Android") > -1; //android终端
      let isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
      this.type = isiOS ? "1" : "0";
    },
    getInfo() {
      let vm = this;
      this.$dialog.loading.open();
      mui.ajax({
        url: ygOnlineShopIndex,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          token: md5(`gjfengygOnlineShopIndex`)
        },
        success(res) {
          vm.$dialog.loading.close();
          vm.info = res.result;
        },
        error(e) {
          vm.$dialog.loading.close();
          vm.$dialog.toast({
            mes: "网络异常，请稍后重试！"
          });
        }
      });
    },
    getTips() {
      let vm = this;
      mui.ajax({
        url: newNotice,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          key: "ALERTS",
          token: md5(`gjfengmemberCooperationRule`)
        },
        success(res) {
          if (res.result) {
            vm.tipsInfo = res.result;
            vm.showTips = true;
          } else {
            vm.showTips = false;
          }
        }
      });
    },
    refresh() {
      let pa = Promise.resolve(this.getInfo());
      let pb = Promise.resolve(this.$store.dispatch("getInfo"));
      Promise.all([pa, pb]).then((a, b) => {
        this.$refs.homepage.$emit("ydui.pullrefresh.finishLoad");
        this.$dialog.notify({
          mes: "已更新",
          timeout: 1500
        });
      });
    },
    getColumn() {
      let vm = this;
      mui.ajax({
        url: allColumn,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          token: md5(`gjfengallColumn`)
        },
        success(res) {
          let _result = res.result;
          let menu = [];
          Object.entries(_result).forEach((value, index) => {
            var item = {};
            var parent = value[0],
              children = value[1];
            if(children.length>0){
              menu.push(...children)
            }
          });
          vm.column = menu;
        }
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
    },
    login() {
      if (!this.member) {
        this.$router.push({ name: "Login" });
        return;
      }
      this.$router.push({ name: "Me" });
    },
    navigate(link, p) {
      //商品列表
      if (p && p.id) {
        this.$router.push({
          name: "Products",
          params: { update: true },
          query: p
        });
      } else {
        //其他平台
        if (!link) {
          this.$dialog.toast({
            mes: "该模块未开发，敬请期待！"
          });
          return;
        }
        this.$router.push({ name: link });
      }
    },
    navigate2(link,p,i){
      this.curIndex = i;
      setTimeout(()=>{
        this.navigate(link,p);
      },500)
    },
    goVocher() {
      this.$router.push({
        name: "Products",
        params: { update: true },
        query: { type: 1, id: this.info.indexProducts[1].columnId }
      });
    },
    getVersion() {
      let vm = this;
      plus.runtime.getProperty(plus.runtime.appid, function(inf) {
        vm.curVersion = inf.version;
        if (vm.type == "0") {
          vm.findUpgrade();
        }
      });
    },
    findUpgrade() {
      let vm = this;
      mui.ajax({
        url: findAppUpgredeByType,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          type: this.type,
          token: md5("gjfengfindAppUpgredeByType")
        },
        success(res) {
          let _result = res.result;
          if (_result.version && vm.curVersion < +_result.version) {
            vm.$store.commit("RECORD_UPDATE", true);
            vm.$dialog.confirm({
              title: `检测到新版本：${_result.version}，是否升级？`,
              mes: `${_result.describe}`,
              opts: () => {
                let wgtUrl = _result.jumpUrl;
                vm.downloadWgt(wgtUrl);
              }
            });
          }
        }
      });
    },
    downloadWgt(url) {
      let vm = this;
      this.$dialog.loading.open("下载更新...");
      plus.downloader
        .createDownload(url, { filename: "_doc/update/" }, function(d, status) {
          if (status == 200) {
            vm.installWgt(d.filename); // 安装更新包
          } else {
            vm.$dialog.alert({
              mes: "下载更新失败！"
            });
          }
          vm.$dialog.loading.close();
        })
        .start();
    },
    installWgt(path) {
      let vm = this;
      this.$dialog.loading.open("安装更新...");
      plus.runtime.install(
        path,
        {},
        function() {
          vm.$dialog.loading.close();
          vm.$dialog.alert({
            mes: "应用资源更新完成！",
            callback: () => {
              plus.runtime.restart();
            }
          });
        },
        function(e) {
          vm.$dialog.loading.close();
          vm.$dialog.alert({
            mes: "安装更新失败[" + e.code + "]：" + e.message
          });
        }
      );
    }
  }
};
</script>
<style lang='less' scoped>
@import "../../style/mixin.less";
.head-top {
  height: 1rem;
  background-color: @white;
  padding: 0.1rem 0.15rem;
  .left-icon img {
    .wh(0.6rem,0.6rem);
    border-radius: 50%;
  }
  .search-container2 {
    background: rgba(232, 232, 232, 0.8);
    padding: 0.15rem 0.1rem 0.15rem 0.1rem;
    border-radius: 0.4rem;
    .mg-h;
    input {
      width: 86%;
      border: none;
      padding-left: 0.1rem;
      font-size: 0.3rem;
    }
  }
  .self-search {
    display: inline-block;
    color: rgb(102, 102, 102);
    width: 0.5rem;
    border-right: 1px solid #ccc;
  }
}
.nav-container {
  background: @white;
  height: 1rem;
  line-height: 1rem;
}
.yd-slider-item img {
  height: 4rem;
}
.high-container {
  background-color: @white;
  margin-top: 3px;
  img {
    width: 100%;
  }
}
.nav-list {
  padding: 0 2%;
  overflow: auto;
  -webkit-overflow-scrolling: touch;
  white-space: nowrap;
  background-color: @white;
  .nav-item {
    display: inline-block;
    width: 20%;
    height: .6rem;
    text-align: center;
    transition: all 0.2s;
    font-size: 0.3rem;
    span{
    border-bottom: 2px solid transparent;

    }
    &.nav-active {
      span{
        color: @red;
      border-color: currentColor;
      padding: 0 0 .1rem 0;
      }
      
    }
  }
}
.platform-container {
  background-color: @white;
  padding: 0 0 0.1rem;
  overflow: hidden;
  .login-btn {
    margin: 0 auto;
    background: #fe6637;
    color: #fff;
    font-size: 0.32rem;
    width: 3rem;
    text-align: center;
    height: 0.8rem;
    line-height: 0.8rem;
    border-radius: 0 0 5px 5px;
  }
  .platform-item {
    .mg-v;
    text-align: center;
    img {
      .wh(1rem,1rem);
      border-radius: 50%;
    }
    p {
      font-size: 0.26rem;
    }
  }
}
.news-box {
  padding: 0 0.1rem;
  margin: 0.1rem 0;
  background-color: @white;
  .rolltip {
    width: 2rem;
    img {
      width: 100%;
    }
  }
  .news-tag {
    display: inline-block;
    margin: 0 0.1rem;
    color: @red;
    border: 1px solid @red;
    border-radius: 10px;
    padding: 1px 5px;
    .text-center;
  }
  .news-title {
    .ellipsis;
  }
}
.fresh-container {
  background-color: @white;
  margin: 0.1rem 0;
  h2 {
    margin: 10px 0;
    font-size: 18px;
    font-weight: 400;
    letter-spacing: 5px;
  }
  .fresh-item {
    width: 47%;
    margin-left: 2%;
    margin-top: 2%;
    img {
      width: 100%;
      border-radius: 5px;
    }
  }
}
.pd-list {
  .pd-item {
    background-color: @white;
    margin-bottom: 0.1rem;
  }
  .pd-item__large {
    width: 100%;
    .pd;
    .img-cover {
      float: left;
      width: 40%;
      img {
        width: 100%;
      }
    }
    .pd-info {
      float: left;
      width: 58%;
      margin-left: 2%;
      .pd-new {
        color: #fc8684;
        font-size: 0.34rem;
        font-weight: bold;
      }
      .pd-name {
        font-size: 0.36rem;
        .multi-ellipsis(2);
      }
      .pd-attr {
        color: @lightgray;
        font-size: 0.26rem;
        .multi-ellipsis(2);
      }
      .pd-price {
        font-size: 0.46rem;
      }
    }
  }
  .pd-item__middle {
    width: 49%;
    padding: 0.1rem;
    .img-cover {
      width: 100%;
      position: relative;
      padding: 50% 0;
      img {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
      }
    }
    .pd-info {
      margin-top: 0.1rem;
      .pd-name {
        height: 40px;
        line-height: 20px;
        font-size: 0.28rem;
        .multi-ellipsis(2);
      }
      .pd-attr {
        color: @lightgray;
        font-size: 0.24rem;
        height: 40px;
        line-height: 20px;
        .multi-ellipsis(2);
      }
      .pd-price {
        font-size: 0.42rem;
      }
    }
    &:nth-of-type(2n) {
      margin-right: 1%;
    }
    &:nth-of-type(2n + 1) {
      margin-left: 1%;
    }
  }
}
.tips-content {
  background-color: @white;
  border-radius: 0.1rem;
  padding: 0.3rem 0.1rem;
  position: relative;
  .close {
    position: absolute;
    top: 0;
    right: 0;
  }
}
</style>