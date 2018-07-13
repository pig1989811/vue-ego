<template>
  <div>
    <yd-navbar title="我的二维码">
      <section slot="left" @click="goBack" style="line-height:1rem;">
        <yd-navbar-back-icon>返回</yd-navbar-back-icon>
      </section>
      <!-- <section slot="right" @click="share" style="line-height:1rem;">
        <span class="iconfont self-daohang primary-color">分享</span>
      </section> -->
    </yd-navbar>
    <main class='scroll-content-2'>
      <section class="qr-container">
        <div class="head-container flex align-center">
          <img :src="getImgPath(member.imgHeadUrl)" alt="头像" class="head-img">
          <p class="flex-1  fs-14">{{member.name||member.nickName||member.mobile}}</p>
          <img :src="getLocalImg('default.png')" alt="" class="logo-img">
        </div>
        <div class="qr-box">
          <img :src="info.imgAppQrUrl" alt="二维码">
        </div>
          <div class="text-center">扫一扫上面的二维码，注册天天易购</div>
          <div class="bottom-box text-center">
            <img :src="getLocalImg('tt_3.png')" alt="">
          </div>
      </section>
    </main>
  </div>
</template>
<script>
import { mapState } from "vuex";
import HeaderTop from "components/header/index";
import { mixin, localImg } from "components/common/mixin";
import { myQr } from "../../api/index";
export default {
  name: "QrCode",
  data() {
    return {
      shares: {},
      info: {}
    };
  },
  components: { HeaderTop },
  computed: { ...mapState(["member"]) },
  mixins: [mixin, localImg],
  created() {
    this.getInfo();
  },
  activated() {
    // this.updateServices();
  },
  methods: {
    getInfo() {
      let vm = this;
      // this.$dialog.loading.open();
      mui.ajax({
        url: myQr,
        type: "post",
        headers: { "app-version": "v1.0" },
        data: {
          account: this.member.mobile,
          token: md5(`gjfengmyQr`)
        },
        success(res) {
          // vm.dialog.loading.close();
          vm.info = res.result;
        }
      });
    },
    showSave() {
      clearTimeout(this.touch);
      this.touch = setTimeout(() => {
        this.$dialog.confirm({
          title: "提示",
          mes: "需要保存图片？",
          opts: () => {
            this.downloadPic();
          }
        });
      }, 500);
    },
    clearShow() {
      clearTimeout(this.touch);
    },
    downloadPic() {
      plus.downloader
        .createDownload(this.info.imgAppQrUrl, {}, (d, status) => {
          if (status == 200) {
            plus.gallery.save(
              d.filename,
              () => {
                alert("已保存到相册");
              },
              () => {
                alert("保存相册失败");
              }
            );
          } else {
            alert("保存异常");
          }
        })
        .start();
    },
    goRecommend() {
      this.$router.push({
        name: "Recommend",
        params: { id: this.member.id }
      });
    },
    goBack() {
      this.$router.go(-1);
    },
    share() {
      let vm = this;
      plus.downloader
        .createDownload(this.info.imgAppQrUrl, {}, (d, status) => {
          if (status == 200) {
            plus.share.sendWithSystem(
              {
                content: "我的二维码",
                pictures: [d.filename],
                href: vm.info.imgAppQrUrl
              },
              () => {},
              e => {}
            );
          } else {
            alert("二维码保存失败");
          }
        })
        .start();
    }
  }
};
</script>
<style lang='less' scoped>
@import "../../style/mixin.less";
.qr-container {
  width: 90%;
  background-color: @white;
  margin: 0.3rem auto 0;
  padding: 0.5rem 0 1.8rem;
  border-radius: 5px;
  .head-container {
    .pd-h;
    .head-img {
      .wh(1.2rem,1.2rem);
      border-radius: 50%;
      margin-right: 0.1rem;
    }
    .logo-img {
      .wh(0.8rem,0.8rem);
      border-radius: 50%;
    }
  }

  .qr-box {
    .wh(5rem,5rem);
    margin: 0 auto;
    user-select: none;
    img {
      width: 100%;
    }
    
  }
  .qr-desc {
      font-size: 0.24rem;
    }
}
.share-container {
  background-color: @white;
  width: 6rem;
  margin: 2rem auto 0;
  padding: 0.3rem 0;
  color: #333;
  h3 {
    margin-bottom: @pd;
  }
  p {
    margin-top: @pd;
  }
  .share-icon {
    display: block;
    margin: 0 auto;
    .wh(30px,30px);
    background-size: contain;
    background-repeat: no-repeat;
  }
}
.bottom-box{
  margin-top: .7rem;
  img{
    width: 80%;
  }
}
</style>