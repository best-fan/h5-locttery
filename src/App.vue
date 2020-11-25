<template>
  <div :class="isScroll ? 'draw' : 'fixed'">
    <div class="page-top">
      <div class="flex-center btn-list">
        <div @click="setPrize(5)">五等份</div>
        <div @click="setPrize(6)">六等份</div>
        <div @click="setPrize(7)">七等份</div>
        <div @click="setPrize(8)">八等份</div>
        <div @click="setPrize(9)">九等份</div>
      </div>
      <div class="flex-center btn-list">
        <div @click="setAnimate(1)">动画一</div>
        <div @click="setAnimate(2)">动画二</div>
    </div>
      <span class="top-integral" v-if="drawData.awardList.length != 0"
        >我的积分：{{ drawData.score }}</span
      >
    </div>
    <div class="page-center">
    <template v-if="animateType==1">
      <lottery 
        ref="draw"
        v-if="drawData.awardList.length != 0"
        :haveScore="drawData.score"
        :haveTime="drawData.drawTimes"
        :drawList="drawData.awardList"
        :drawScore="drawData.costScore"
        @draw="doDraw"
        @lotteryResult="lottery"
      ></lottery>
    </template>
    <template v-else>
           <loteryNomer 
        ref="draw"
        v-if="drawData.awardList.length != 0"
        :haveScore="drawData.score"
        :haveTime="drawData.drawTimes"
        :drawList="drawData.awardList"
        :drawScore="drawData.costScore"
        @draw="doDraw"
        @lotteryResult="lottery"
      ></loteryNomer>
    </template>
      <div
        class="draw-number flex-center"
        v-if="drawData.awardList.length != 0"
      >
        剩余抽奖次数
        <span>{{ drawData.drawTimes }}</span>
      </div>
      <div class="winners-list" v-if="drawData.winnerList">
        <span class="list-title">获奖名单</span>
        <div class="list-div">
          <div class="list-item-bg">
            <div class="item-title">
              <span>昵称</span>
              <span>手机号</span>
              <span>奖品</span>
            </div>
            <div><scrollText :listData="drawData.winnerList"></scrollText></div>
          </div>
        </div>
        <div @click="openRecordPopup" class="draw-text flex-center">
          我的抽奖记录
        </div>
      </div>
    </div>
    <div class="page-bottom" v-if="drawData.remark">
      <span class="draw-describe-title">抽奖说明</span>
      <span class="text">{{ drawData.remark }}</span>
    </div>
  </div>
</template>

<script>
import lottery from "./components/lottery.vue";
import loteryNomer from './components/lottery-nomer';
import scrollText from "./components/scrollingText.vue";

var toast;
export default {
  components: {
  loteryNomer,
    lottery,
    scrollText,
  },
  data() {
    return {
      myDrawList: [], //抽奖列表
      winnerList: [], //中奖记录列表
      myRecord: [], //我的中奖记录列表

      drawData: {
        awardList: [],
        score: 0,
        drawTimes: 0,
      },
      drawResult: {
        alertMsg: false,
        id: "001",
        prizeName: "谢谢参与",
        times: 99,
        type: 1,
      }, //抽奖结果
      isScroll: true,
      animateType:1,
    };
  },
  mounted() {
    this.getDrawData();
  },
  created() {
    document.title = "积分抽奖";
  },
  methods: {
    setAnimate(e){
this.animateType=e;
    },
    setPrize(e) {
      let drawList = [
        {
          acitvityAwardId: "1",
          imagePath:
            "/group1/UIMG/20201106/00495747-8bd2-4f4a-936d-ec12a39d32c6.jpg",
          awardName: "纸巾加肥皂112",
        },
        {
          acitvityAwardId: "2",
          imagePath:
            "/group1/UIMG/20201106/bcf8f351-a726-4786-9001-ae05e9af3d8c.jpg",
          awardName: "谢谢参与",
        },
        {
          acitvityAwardId: "3",
          imagePath:
            "/group1/UIMG/20201106/44d47cec-533f-4c11-b64e-ce8802a0aabd.jpg",
          awardName: "4积分",
        },
        {
          acitvityAwardId: "4",
          imagePath:
            "/group1/UIMG/20201106/d5c3e32b-eef0-44a4-93df-a606acdc7144.jpg",
          awardName: "10元",
        },
        {
          acitvityAwardId: "5",
          imagePath:
            "https://vm.gtimg.cn/tencentvideo/vstyle/web/v4/style/img/common/site_head/head_logo_gold.png",
          awardName: "会员",
        },
        {
          acitvityAwardId: "6",
          imagePath:
            "https://vm.gtimg.cn/tencentvideo/vstyle/web/v4/style/img/common/site_head/head_logo_gold.png",
          awardName: "谢谢参与",
        },
        {
          acitvityAwardId: "7",
          imagePath:
            "https://vm.gtimg.cn/tencentvideo/vstyle/web/v4/style/img/common/site_head/head_logo_gold.png",
          awardName: "1000元代金券",
        },
        {
          acitvityAwardId: "8",
          imagePath:
            "https://vm.gtimg.cn/tencentvideo/vstyle/web/v4/style/img/common/site_head/head_logo_gold.png",
          awardName: "10元优惠券",
        },
        {
          acitvityAwardId: "9",
          imagePath:
            "https://vm.gtimg.cn/tencentvideo/vstyle/web/v4/style/img/common/site_head/head_logo_gold.png",
          awardName: "50元优惠券",
        },
      ];
      drawList.length = e;
      this.drawData.awardList = drawList;
    },
    //抽奖页面 信息
    getDrawData() {
      let that = this;
      let res = {
        respCode: "200",
        respDesc: "获取成功",
        attribute: {
          data: {
            score: 400,
            winnerList: [
              {
                name: "卓*",
                mobile: "153****0239",
                prize: "会员",
                account: "411004198702147870",
              },
              {
                name: "申***",
                mobile: "137****5328",
                prize: "会员",
                account: "421428198212282349",
              },
              {
                name: "子***",
                mobile: "135****3753",
                prize: "会员",
                account: "461401198509051868",
              },
              {
                name: "张**",
                mobile: "138****6002",
                prize: "会员",
                account: "412901197001012034",
              },
              {
                name: "姜*",
                mobile: "150****3662",
                prize: "会员",
                account: "420504198604179036",
              },
              {
                name: "竺*",
                mobile: "136****2169",
                prize: "会员",
                account: "430304199411184291",
              },
              {
                name: "宣*",
                mobile: "131****4296",
                prize: "会员",
                account: "420220198912028872",
              },
              {
                name: "毋**",
                mobile: "134****6128",
                prize: "会员",
                account: "410717198205018624",
              },
              {
                name: "松**",
                mobile: "138****3504",
                prize: "会员",
                account: "421515199012142353",
              },
              {
                name: "令***",
                mobile: "135****9068",
                prize: "会员",
                account: "460404199510263419",
              },
              {
                name: "胥*",
                mobile: "137****1106",
                prize: "会员",
                account: "420721198307251327",
              },
              {
                name: "冒*",
                mobile: "137****8109",
                prize: "会员",
                account: "411702197608083098",
              },
              {
                name: "太**",
                mobile: "157****0603",
                prize: "会员",
                account: "420809197602139728",
              },
              {
                name: "种**",
                mobile: "155****5934",
                prize: "会员",
                account: "441620198302241050",
              },
              {
                name: "费*",
                mobile: "155****5548",
                prize: "会员",
                account: "420705198708047026",
              },
              {
                name: "家**",
                mobile: "139****6468",
                prize: "会员",
                account: "451123197408096540",
              },
              {
                name: "贾*",
                mobile: "131****6559",
                prize: "会员",
                account: "421401199109188701",
              },
              {
                name: "兀***",
                mobile: "155****8738",
                prize: "会员",
                account: "421118197810101294",
              },
              {
                name: "于*",
                mobile: "151****8347",
                prize: "会员",
                account: "460816198604035041",
              },
              {
                name: "郦*",
                mobile: "151****3161",
                prize: "会员",
                account: "430623197706135141",
              },
              {
                name: "种**",
                mobile: "153****1100",
                prize: "会员",
                account: "421214197009212058",
              },
              {
                name: "法**",
                mobile: "150****7521",
                prize: "会员",
                account: "450828198308074713",
              },
              {
                name: "覃**",
                mobile: "139****8852",
                prize: "会员",
                account: "411009198212307900",
              },
              {
                name: "巢**",
                mobile: "137****0364",
                prize: "会员",
                account: "441113199312172543",
              },
              {
                name: "王*",
                mobile: "132****7303",
                prize: "会员",
                account: "441503197909125270",
              },
            ],
            costScore: 1,
            drawTimes: 29995,
            divideSize: 5,
            awardList: [
              {
                acitvityAwardId: "1",
                imagePath:
                  "/group1/UIMG/20201106/00495747-8bd2-4f4a-936d-ec12a39d32c6.jpg",
                awardName: "纸巾加肥皂112",
              },
              {
                acitvityAwardId: "2",
                imagePath:
                  "/group1/UIMG/20201106/bcf8f351-a726-4786-9001-ae05e9af3d8c.jpg",
                awardName: "谢谢参与",
              },
              {
                acitvityAwardId: "3",
                imagePath:
                  "/group1/UIMG/20201106/44d47cec-533f-4c11-b64e-ce8802a0aabd.jpg",
                awardName: "4积分",
              },
              {
                acitvityAwardId: "4",
                imagePath:
                  "/group1/UIMG/20201106/d5c3e32b-eef0-44a4-93df-a606acdc7144.jpg",
                awardName: "10元",
              },
              {
                acitvityAwardId: "5",
                imagePath:
                  "https://vm.gtimg.cn/tencentvideo/vstyle/web/v4/style/img/common/site_head/head_logo_gold.png",
                awardName: "会员",
              },
            ],
          },
        },
        success: false,
      };

      that.drawData = res.attribute.data;
    },
    openRecordPopup(e) {
      alert("中奖记录");
    },

    //我的中奖记录列表
    getMyPrizeData(e) {
      alert("中奖记录");
    },

    //点击抽奖回调
    doDraw(e) {
      let that = this;
      // console.log('不能抽奖', e);
      if (
        that.drawData.drawTimes == 0 &&
        that.drawData.score - that.drawData.costScore >= 0
      ) {
        //有积分 且没有抽奖次数  去分享
        that.getShareStatus(true);
        that.$refs.draw.drawRotate(0, true);
        return;
      } else if (that.drawData.score - that.drawData.costScore < 0) {
        alert("积分不足！");
        that.$refs.draw.drawRotate(0, true);
        return;
      }
      that.postDraw();
    },
    //发送抽奖请求
    postDraw() {
      let that = this;

      let res = {
        attribute: {
          data: {
            id: "5",
          },
        },
      };
      that.drawResult = res.attribute.data;
      that.drawResult.id = Math.floor(
        Math.random() * that.drawData.awardList.length + 1
      );
      console.log('中奖Id',that.drawResult.id);

      let index = 0;
      that.drawData.awardList.map((itm, idx) => {
        if (itm.acitvityAwardId == that.drawResult.id) {
          that.drawResult.awardName = that.drawData.awardList[idx].awardName;
          index = idx + 1;
          return;
        }
      });
      that.drawData.score = that.drawData.score - that.drawData.costScore;
      that.drawData.drawTimes = that.drawData.drawTimes - 1;
      that.$refs.draw.drawRotate(index);
    },
    //中奖结果回调
    lottery() {
      let  that=this;
      setTimeout(function(){
       alert(that.drawResult.awardName)
      },500)
      console.log("中奖结果回调:", this.drawResult.awardName);
    },
  },
};
</script>

<style lang="scss" scoped>
.btn-list {
  position: relative;
  z-index: 2;
  div {
    color: white;
    margin: 0 4px;
  }
}
.draw {
  position: relative;
  z-index: 1;
  min-height: 100vh;
  padding-bottom: 35px;
  background-image: linear-gradient(180deg, #f45041 0%, #f58a3f 100%);
}
.fixed {
  position: fixed;
  width: 100vw;
  height: 100vh;
  background-image: linear-gradient(180deg, #f45041 0%, #f58a3f 100%);
}

.page-top {
  padding-top: 15px;

  .top-integral {
    position: relative;
    z-index: 1;
    display: block;
    width: 180px;
    height: 38px;
    text-align: center;
    margin: 0 auto;
    background-image: linear-gradient(180deg, #ffab5b 0%, #ff822a 100%);
    box-shadow: 0px -1px 4px 0px rgba(227, 36, 22, 0.36);
    border-radius: 21px;
    border: solid 1px #e55145;
    font-size: 15px;
    color: #ffffff;
    line-height: 35px;
    margin-top: 22px;
  }
}
.page-center {
  margin-top: 11px;
  // margin-top: 31px;
  .draw-number {
    // display: block;
    width: 150px;
    height: 30px;
    font-size: 13px;
    background-color: #0000007f;
    border-radius: 17px;
    text-align: center;
    // line-height: 30px;
    color: #ffffff;
    margin: 10px auto;
    span {
      display: block;
      margin-left: 4px;
      color: #fff35c;
    }
  }
  .winners-list {
    text-align: center;
    .list-title {
      font-size: 16px;
      color: #ffffff;
    }
    .list-div {
      width: 345px;
      height: 177px;
      margin: 0 auto;
      margin-top: 12px;
      border: 1px solid #e55145;
      border-radius: 10px;
      .list-item-bg {
        width: 325px;
        height: 157px;
        background: #e55145;
        border-radius: 10px;
        margin: auto;
        margin-top: 10px;
        .item-title {
          display: flex;
          justify-content: space-between;
          text-align: left;
          padding-top: 15px;
          margin-left: 23px;
          margin-right: 11px;
          span {
            display: block;
            font-size: 16px;
            color: #ffffff;
          }
          span:nth-child(1) {
            width: 50px;
          }
          span:nth-child(2) {
            width: 80px;
          }
          span:nth-child(3) {
            width: 90px;
          }
        }
      }
    }
    .draw-text {
      // display: block;

      width: 345px;
      height: 48px;
      // text-align: center;
      // line-height: 48px;
      font-size: 15px;
      color: #ffffff;
      margin: 0 auto;
      margin-top: 20px;
      background-image: linear-gradient(270deg, #e32416 0%, #e32416 100%);
      border-radius: 8px;
      border-radius: 8px;
    }
  }
}
.page-bottom {
  .draw-describe-title {
    display: block;
    text-align: center;
    margin: 22px auto 9px;
    font-size: 16px;
    color: #ffffff;
  }
  .text {
    display: block;
    width: 347px;
    margin: 0 auto;
    font-size: 13px;
    color: #f5f5f5;
  }
}
//全局
.top-integral:active,
.draw-text:active,
.btn:active {
  opacity: 0.8;
}
.flex {
  display: flex;
}
.flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
