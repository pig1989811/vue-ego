<template>
  <div>
    <header class="head-top flex align-center">
      <!-- <div class="left-icon text-center" @click="login">
        <span class="iconfont-large self-me1"></span>
      </div> -->
      <div class="search-container2 flex-1" @click="goProducts('1398')">
        <span class="iconfont self-search"></span>
        <input type="text" disabled placeholder="搜索自营商品...">
      </div>
      <!-- <div class="right-icon text-center" @click="scan">
        <span class="iconfont-large self-scan"></span>
      </div> -->
      <div class="right-icon text-center">
        <router-link to="/news/index?update=true">
          <span class="iconfont-large self-message"></span>
        </router-link>
      </div>
    </header>
    <main class='scroll-content'>
      <yd-pullrefresh ref="homepage" :callback="refresh" :show-init-tip="false">
        <section>
          <yd-slider :loop="false" :autoplay="2000">
            <yd-slider-item v-for="(item,index) in info.indexAds" :key="item.id">
              <router-link :to="{name:'Product',query:{id:item.address.substring(item.address.lastIndexOf('/')+1),tips:0}}" v-if="index==0">
                <img :src="item.photo" :alt="item.names">
              </router-link>
            </yd-slider-item>
            <yd-slider-item @click.native="goMerchant">
              <img :src="getLocalImg('tt_2.jpg')" alt="采购专区">
            </yd-slider-item>
          </yd-slider>
        </section>
        <!-- 四大平台 -->
        <section class="platform-container flex just-around">
          <div class="discount-tag">优惠购</div>
          <div v-for="(item,index) in platform" :key="index" class="platform-item flex-1" @click="navigate(item.link)">
            <img :src="getLocalImg(item.img)">
            <p>{{item.text}}</p>
          </div>
        </section>
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
          <router-link :to="{name:'News',query:{update:'true'}}">更多></router-link>
        </div>
        <section class="high-container">
          <img :src="getLocalImg('20180529_1.png')" alt="直降到底 嗨翻618">
        </section>
        <section class="hot-container">
          <div class="hot-box">
            <div class="hot-header">
              <img :src="getLocalImg('20180529_4.png')" alt="">
              <span>今日必抢</span>
            </div>
            <ul class="type-list clearfix">
              <li class="type-item pd-item fl">
                <div class="img-cover">
                  <img src="http://admin.ttyg168.cn/public/upload/goods/2018/04-04/1d1f309ca9fab0d3e6119d9cc22860ad.jpg">
                </div>
                <div class="type-title jianbian-bg1">必备厨具</div>
              </li>
              <li class="type-item pd-item fl">
                <div class="img-cover">
                  <img src="http://admin.ttyg168.cn/public/upload/goods/2018/04-04/1d1f309ca9fab0d3e6119d9cc22860ad.jpg">
                </div>
                <div class="type-title jianbian-bg1">家用电器</div>
              </li>
              <li class="type-item pd-item fl">
                <div class="img-cover">
                  <img src="http://admin.ttyg168.cn/public/upload/goods/2018/04-04/1d1f309ca9fab0d3e6119d9cc22860ad.jpg">
                </div>
                <div class="type-title jianbian-bg1">酒水副食</div>
              </li>
            </ul>
          </div>
          <template v-for="(item,index) in info.indexProducts.slice(0,2)">
            <div :key="index" class="hot-box">
              <!-- <div :style="{'background-image':formatBg('20180529_4.png')}" class="hot-header">
                <span>{{item.columnName}}</span>
              </div> -->
              <div class="hot-header">
                <img :src="getLocalImg('20180529_4.png')" alt="">
                <span>{{item.columnName}}</span>
              </div>
              <ul class="pd-list clearfix">
                <router-link :to="{path:'/online/product',query:{id:pd.id}}" v-for="pd in item.product" :key="pd.id" tag="li" class="pd-item fl">
                  <div class="img-cover">
                    <img :src="pd.imgUrl" :alt="pd.name">
                  </div>
                  <div class="title">
                    <h3>{{pd.name}}</h3>
                  </div>
                  <div class="price">
                    零售价:
                    <span class="orange-color fs-15">￥{{pd.price}}</span>
                  </div>
                </router-link>
              </ul>
            </div>
          </template>

        </section>
        <!-- 抢购专区 -->
        <!-- <section class="buy-container">
        <div class="flex just-between align-center">
          <div class="flex align-center">
            <h3 class="danger-color">限时限量抢购</h3> -->
        <!-- <p v-show="!(curTime==3||curTime==4)"><span>{{t.hours}}</span>:<span>{{t.minutes}}</span>:<span>{{t.seconds}}</span></p> -->
        <!-- </div>
          <p class="danger-color" @click="goProducts(info.indexProducts&&info.indexProducts[0].columnId)">更多>>></p> -->
        <!-- <p class="danger-color"  @click="goProducts(info.indexProducts&&info.indexProducts[0].columnId)">{{nearHour[curTime]}}</p> -->
        <!-- </div>
        <ul class="flex just-between pd-list">
            <li class="pd-item" v-for="(item,index) in info.indexProducts&&info.indexProducts[0].product" :key="index">
              <router-link :to="{path:'/online/product',query:{id:item.id}}">
                <div class="pd-img"><img :src="item.imgUrl" :alt="item.name"></div> -->
        <!-- <p class="danger-color fs-14">￥{{item.price}}</p> -->
        <!-- </router-link>
            </li>
          </ul> -->
        <!-- <div class="disable-mask" v-show="curTime==3||curTime==4"></div> -->
        <!-- </section> -->
        <!-- 代金券 -->
        <!-- <section class="vocher-container">
        <div>
          <div class="flex just-between align-center" @click="goVocher">
            <p>代金券兑换入口
            </p>
            <span class="entry">进入>>></span>
          </div>
          <ul class="flex just-between pd-list">
            <li class="pd-item" v-for="(item,index) in info.indexProducts&&info.indexProducts[1].product" :key="index">
              <router-link :to="{path:'/online/product',query:{id:item.id}}">
                <img :src="item.imgUrl" :alt="item.name" class="pd-img">
              </router-link>
              <p class="buy-now">热销</p>
            </li>
          </ul>
        </div>
      </section> -->
        <!-- <section class="product-container" v-for="(item,index) in info.indexProducts&&info.indexProducts.slice(2)" :key="index">
          <h3 class="type-title" @click="goProducts(item.columnId)">{{item.columnName.substr(0,2)}}
            <span :style="{color:colors[index]}">{{item.columnName.substr(2)}}>>></span>
          </h3>
          <ul class="pd-list flex">
            <router-link :to="{path:'/online/product',query:{id:pd.id}}" v-for="pd in item.product" :key="pd.id" tag="li" class="pd-item">
              <div class="img-cover">
                <img :src="pd.imgUrl" :alt="pd.name">
              </div>
              <div class="title flex align-center">
                <h3>{{pd.name}}</h3>
              </div>
              <div class="price flex align-center">
                零售价:
                <span class="orange-color fs-15">￥{{pd.price}}</span>
              </div>
            </router-link>
          </ul>
        </section> -->
        <section class="activity-container clearfix">
          <div class="activity-header" :style="{'background-image':formatBg('20180529_2.png')}">
            <span class="jianbian-text1">爱生活</span>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">超值家电</h3>
              <p>美的大功率吸尘器</p>
              <span class="activity-desc jianbian-bg2">领券购物更优惠</span>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">精选厨具</h3>
              <p>美的大功率吸尘器</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">开胃饮料</h3>
              <p>SO有机苹果醋</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">男士理容</h3>
              <p>飞科全身水洗电动剃须刀</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
        </section>
        <section class="activity-container clearfix">
          <div class="activity-header" :style="{'background-image':formatBg('20180529_2.png')}">
            <span class="jianbian-text1">购好物</span>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">就爱高颜值</h3>
              <p>美的大功率吸尘器</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">美容护肤</h3>
              <p>白藜芦醇温润丰唇膏</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">大牌特价</h3>
              <p>TCL多功能家用电烤箱</p>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
          <div class="activity-item fl">
            <div class="activity-text fl">
              <h3 class="activity-name">送礼首选</h3>
              <p>贵州茅台天朝上品贵人酒</p>
              <span class="activity-desc jianbian-bg2">好物低价任你抢</span>
            </div>
            <div class="activity-thumb fr">
              <img src="http://statics.76sd.com/data/files/goods/20180111/5a56b972287ad.jpg" alt="">
            </div>
          </div>
        </section>
        <section class="merchant-container">
          <img :src="getLocalImg('20180529_3.jpg')" alt="开通跨行业盈利系统">
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
  newNotice
} from "../../api/index";
export default {
  name: "Online",
  data() {
    return {
      oldBack: mui.back,
      info: {},
      loginAccount: false,
      platform: [
        { img: "20180529_6.png", link: "/online/tmindex", text: "淘宝" },
        { img: "20180529_9.png", link: "/online/tmindex", text: "天猫" },
        { img: "20180529_8.png", link: "/online/jdindex", text: "京东" },
        { img: "20180529_7.png", link: "/online/ttindex", text: "天天易购" },
        { img: "20180529_5.png", link: "/online/allcolumn", text: "所有分类" }
      ],
      colors: ["#dc0719", "#da5906", "#ff3964"],
      type: "", //APP环境
      curVersion: "", //app版本
      showTips: false,
      tipsInfo: {},
      t: {
        hours: "00",
        minutes: "00",
        seconds: "00"
      },
      nearHour: [
        "9:30-10:30场",
        "12:30-13:30场",
        "19:30-20:30场",
        "未开始",
        "明天见"
      ],
      curTime: 0
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
    mui.back = this.oldBack;
    next();
  },
  created() {
    this.init();
    this.getInfo();
    this.getTips();
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
    //this.judgeTime();
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
      //this.judgeTime();
    },
    format(t) {
      let h = Math.floor(t / (60 * 60)),
        m = Math.floor(t / 60) % 60,
        s = t % 60,
        pad = t => {
          return t.toString().replace(/^(\d)$/, "0$1");
        };
      h = pad(h);
      m = pad(m);
      s = pad(s);
      return { hours: h, minutes: m, seconds: s };
    },
    countTime(t, callback) {
      let timer = setInterval(() => {
        let { hours, minutes, seconds } = this.format(t--);
        this.t = Object.assign({}, this.t, { hours, minutes, seconds });
        if (t < 0) {
          callback && callback();
          clearInterval(timer);
          timer = null;
        }
      }, 1000);
    },
    judgeTime() {
      var ct = new Date();
      var cy = ct.getFullYear(),
        cm = ct.getMonth() + 1,
        cd = ct.getDate();
      var t_now = ct.getTime(),
        t_930 = new Date(`${cy}-${cm}-${cd} 09:30:00`).getTime(),
        t_1030 = new Date(`${cy}-${cm}-${cd} 10:30:00`).getTime(),
        t_1230 = new Date(`${cy}-${cm}-${cd} 12:30:00`).getTime(),
        t_1330 = new Date(`${cy}-${cm}-${cd} 13:30:00`).getTime(),
        t_1930 = new Date(`${cy}-${cm}-${cd} 19:30:00`).getTime(),
        t_2030 = new Date(`${cy}-${cm}-${cd} 20:30:00`).getTime();
      var cb = () => {
        this.curTime = 3;
      };
      // 未开始
      if (
        t_now < t_930 ||
        (t_now > t_1030 && t_now < t_1230) ||
        (t_now > t_1330 && t_now < t_1930)
      ) {
        this.curTime = 3;
      } else if (t_now >= t_930 && t_now <= t_1030) {
        // 9:30-10:30
        this.curTime = 0;
        this.countTime(Math.floor((t_1030 - t_now) / 1000), cb);
      } else if (t_now >= t_1230 && t_now <= t_1330) {
        // 12:30-13:30
        this.curTime = 1;
        this.countTime(Math.floor((t_1330 - t_now) / 1000), cb);
      } else if (t_now >= t_1930 && t_now <= t_2030) {
        // 19:30-20:30
        this.curTime = 2;
        this.countTime(Math.floor((t_2030 - t_now) / 1000), cb);
      } else {
        // >20:30
        this.curTime = 4;
      }
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
    scan() {
      this.$router.push({ name: "Scan" });
    },
    navigate(link) {
      if (!link) {
        this.$dialog.toast({
          mes: "数据对接中，敬请期待！"
        });
        return;
      }
      this.$router.push({ path: link });
    },
    goVocher() {
      this.$router.push({
        name: "Products",
        params: { update: true },
        query: { type: 1, id: this.info.indexProducts[1].columnId }
      });
    },
    goProducts(id) {
      this.$router.push({
        name: "Products",
        params: { update: true },
        query: { type: 1, id }
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
  .left-icon,
  .right-icon {
    .wh(1rem,1rem);
    line-height: 1rem;
  }
  .search-container2 {
    background: rgb(232, 232, 232);
    padding: 0.15rem 0.1rem 0.15rem 0.1rem;
    border-radius: 0.4rem;
    input {
      width: 86%;
      border: none;
      padding-left: 0.1rem;
      font-size: 0.3rem;
    }
  }
  span {
    color: rgb(102, 102, 102);
  }
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
.buy-container {
  position: relative;
  padding: @pd @pd 0;
  background-color: @white;
  h3 {
    font-size: 0.34rem;
    margin-right: 0.1rem;
  }
  span {
    display: inline-block;
    color: @white;
    background-color: #333;
    width: 15px;
    height: 15px;
    line-height: 15px;
    text-align: center;
  }
  .pd-item {
    a {
      display: block;
    }
    width: 33.3%;
    padding: 0.1rem;
    margin-bottom: 0.1rem;
    text-align: center;
    .pd-img {
      position: relative;
      width: 100%;
      overflow: hidden;
      padding: 50% 0;
      img {
        width: 100%;
        .hv-cen;
      }
    }
  }
  .disable-mask {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    z-index: 500;
  }
}
.platform-container {
  background-color: @white;
  padding: 0.5rem 0 0.1rem;
  position: relative;
  overflow: hidden;
  .discount-tag {
    position: absolute;
    left: -0.15rem;
    top: 0;
    border-radius: 10px;
    background-color: rgb(228, 1, 12);
    color: @white;
    font-size: 0.24rem;
    padding: 2px 0.1rem;
    width: 1rem;
    text-align: right;
  }
  .platform-item {
    margin-bottom: @pd;
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
  background-color: @white;
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
.vocher-container {
  background-color: @white;
  padding: 0.1rem;
  > div {
    background-color: rgb(255, 224, 229);
    border-radius: 5px;
    .pd;
    color: rgb(223, 37, 48);
    p {
      font-size: 0.4rem;
    }
    span {
      font-size: 0.28rem;
    }
  }
  .pd-list {
    margin-top: @pd;
    .pd-item {
      position: relative;
      width: 31%;
      border-radius: @pd;
      text-align: center;
      overflow: hidden;
      a {
        display: block;
      }
      .pd-img {
        box-shadow: 0 0 5px rgba(223, 37, 48, 0.6);
        border-radius: @pd;
        .wh(100%,2rem);
      }
      .buy-now {
        position: absolute;
        left: -0.5rem;
        top: 0.1rem;
        .wh(1.5rem,0.3rem);
        line-height: 0.3rem;
        background-color: @red;
        color: @white;
        font-size: 0.2rem;
        transform: rotate(-45deg);
      }
    }
  }
}
.product-container {
  background-color: @white;
  .pd-list {
    background-color: rgb(243, 243, 243);
  }
  .type-title {
    .pd;
    background-color: rgb(243, 243, 243);
  }
  .pd-item {
    width: 32%;
    margin: 2px;
    background-color: rgb(243, 243, 243);
    .img-cover {
      position: relative;
      width: 100%;
      overflow: hidden;
      padding: 50% 0;
      background-color: @white;
      border: 1px solid @white;
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
        padding: 0 2px;
        font-size: 0.26rem;
        font-weight: normal;
        word-wrap: break-word;
        word-break: break-all;
        color: #333;
      }
    }
    .price {
      margin-top: 0.1rem;
      font-size: 0.2rem;
      .just-cont(flex-end);
    }
  }
}
.activity-container {
  background-color: rgb(242, 242, 242);
  .activity-header {
    .pd-v;
    font-size: 18px;
    font-weight: bold;
    letter-spacing: 5px;
    text-align: center;
    background-size: 100%;
    background-repeat: no-repeat;
  }
  .activity-item {
    width: 50%;
    min-height: 2rem;
    padding: 10px 0;
    border-right: 1px solid #eee;
    border-bottom: 1px solid #eee;
    background-color: @white;
    box-shadow: 0 0 1px rgba(120, 120, 120, 0.3);
    &:nth-of-type(2n+1) {
      border-right: none;
    }
    .activity-text {
      width: 60%;
      padding-left: 8px;
      
      p {
        font-size: 10px;
        color: rgb(153, 153, 153);
        margin:5px 0;
      }
      .activity-desc {
        color: #fff;
        font-size: 8px;
        padding: 3px 5px;
        border-radius: 3px;
      }
    }
    .activity-thumb {
      width: 40%;
      img {
        width: 100%;
        padding-right: 8px;
      }
    }
    .activity-name {
      font-size: 16px;
    }
  }
}
.merchant-container {
  .pd;
  img {
    width: 100%;
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
.jianbian-bg1 {
  background: -webkit-linear-gradient(left, rgb(252, 10, 34), rgb(242, 134, 0));
}
.jianbian-bg2 {
  background: -webkit-linear-gradient(
    left,
    rgb(248, 44, 131),
    rgb(250, 92, 65)
  );
}
.jianbian-text1 {
  display: inline-block;
  background: -webkit-linear-gradient(left, rgb(73, 38, 180), rgb(7, 158, 227));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
.hot-container {
  padding-bottom: @pd;
  background-color: rgb(254, 30, 54);
  .hot-box {
    .hot-header {
      position: relative;
      color: @white;
      font-weight: bold;
      text-align: center;
      img {
        .wh(4rem,0.8rem);
      }

      span {
        .hv-cen;
        font-size: 0.32rem;
        z-index: 5;
      }
    }
  }
  .pd-item {
    width: 30.6%;
    margin-left: 2%;
    padding: 0.1rem;
    background-color: @white;
    .img-cover {
      position: relative;
      width: 100%;
      overflow: hidden;
      padding: 50% 0;
      background-color: @white;
      border-radius: 2px;
      img {
        width: 100%;
        .hv-cen;
        border-radius: 2px;
      }
    }
    .title {
      height: 40px;
      line-height: 20px;
      h3 {
        .multi-ellipsis(2);
        padding: 0 2px;
        font-size: 0.26rem;
        font-weight: normal;
        word-wrap: break-word;
        word-break: break-all;
        color: #333;
      }
    }
    .price {
      margin-top: 0.1rem;
      font-size: 0.2rem;
      text-align: right;
    }
  }
  .type-item {
    .type-title {
      color: @white;
      font-size: 0.28rem;
      font-weight: 700;
      text-align: center;
      border-radius: 0.4rem;
      margin: 0.2rem;
      padding: 3px 0;
    }
  }
}
</style>