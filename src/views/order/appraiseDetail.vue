<template>
  <!-- 评价页面 -->
  <div class="paySuccess-container">
    <div style="position:relative;">
        <Header :title="title"></Header>
    </div>
    <div style="padding: 1rem; background: #fff; display: flex;align-items: center;">
        <div>
            <img style="width: 6rem;" :src="cargo.picture"/>
        </div>
        <div style="flex: 1; padding: 0.5rem;">
            <div style="font-size: 1.22rem;">
              {{cargo.name}}
            </div><br>
        </div>
        <div>
            <img style="width: 6rem;" :src="member.avatar"/>
        </div>
        <div style="flex: 1; padding: 0.5rem;">
            <div style="font-size: 1.22rem;">
              {{member.name}}
            </div><br>
            <div style="padding: 0rem;">
                <!-- <span v-for="(item, index) in redStar" :key="index" style="color:#F56C6C;" @click="redStarClick(index)"> ♥ </span>
                <span v-for="(item, index) in whiteStar" :key="index" style="color:#909399;" @click="whiteStarClick(index)"> ♥ </span> -->
                <span v-for="(item, index) in star" :key="index" @click="starClick(index)">
                    <span v-show="item.checked">  <img width="30rem" src="@/assets/image/star01.png"/> </span>
                    <span v-show="!item.checked"> <img width="30rem" src="@/assets/image/star02.png"/> </span>
                </span>
                <span>{{appraise.createTime}}</span>
            </div>
            <div>
                {{appraise.remark}}
            </div>
        </div>
    </div>
    <div style="padding: 1rem; background: #fff; display: flex;align-items: center;">
        回复
    </div>
  </div>
</template>

<script>
import Header from '@/components/Header'
import { saveAppraise, detail, myInfo, getAppraiseById, getMemberById } from '@/api/index'
export default {
  name: 'paySuccess',
  components: { Header },
  data() {
    return {
      title: '评价晒单',
      star: [
          {checked: true},
          {checked: true},
          {checked: true},
          {checked: true},
          {checked: true}
      ],
      describe: '',
      cargoId: '',
      appraiseId: '',
      memberId: '',
      orderId: '',
      cargo: {},
      appraise: {},
      member: {}
    }
  },
  created() {
    this.appraiseId = this.$route.query.appraiseId;
    this.getAppraise();
  },
  mounted() {
    document.title = '评价晒单'
  },
  methods: {
    // 获取用户信息
    myInfo() {
      myInfo().then(res => {
        this.memberId = res.data.id
      })
    },
    goMall() {
      this.$router.push('/index')
    },
    goOrder() {
      this.$router.push('/order')
    },
    goCargoDetail() {
      this.$router.push('/detail?id=f16db92bf6c7f8ba0e820f70c6ef9d42');
    },
    starClick(val) {
        this.star.forEach((item, index) => {
            if (index > val) {
                item.checked = false;
            } else {
                item.checked = true;
            }
        });
    },
    saveAppraise() {
        let star = 0;
        let vm = this;
        this.star.forEach(item => {
            if (item.checked) {
                star++;
            }
        });

        saveAppraise({
          star: star,
          remark: vm.describe,
          cargoId: vm.cargoId,
          orderId: vm.orderId,
          memberId: vm.memberId
        }).then(res => {
          if (res.code === 200) {
            alert('评价成功！');
            vm.$router.push('/appraiseCenter');
          } else {
            alert('评价失败！');
          }
        })
    },
    getCargoDetail(cargoId) {
      detail({
        id: cargoId
      }).then(res => {
        this.cargo = res.data
        // if (this.goodsInfo.mov) {
        //   this.type = 'video'
        // } else {
        //   this.type = 'image'
        // }
        // this.skuId = this.goodsInfo.sku[0].id
        // this.inventory = this.goodsInfo.sku[0].inventory
      })
    },
    getAppraise() {
        getAppraiseById({
            id: this.appraiseId
        }).then(res => {
            console.error('appraise', res);
            this.appraise = res.data;
            this.getCargoDetail(res.data.cargoId);
            this.getMemberById(res.data.memberId);
        });
    },
    getMemberById(memberId) {
        getMemberById({
            id: memberId
        }).then(res => {
            console.error(res.data);
            this.member = res.data;
        });
    }
  }
}
</script>

<style lang="less" scoped>
.paySuccess-container {
  min-height: 100vh;
  background: #f7f7f7;
  z-index: 9999;
  position: absolute;
  top: 0;
  width: 100%;
  .option {
    padding: 0.94rem;
    .icon {
      font-size: 1.16rem;
      color: #333333;
      text-align: center;
      margin-top: 5.32rem;
      margin-bottom: 4.28rem;
      img {
        width: 5rem;
        height: 5rem;
        margin-bottom: 0.75rem;
      }
    }
    .btn {
      button {
        font-size: 1.06rem;
        color: #ffffff;
        background: linear-gradient(to right, #0095FE, #0066FE);
        height: 2.75rem;
        line-height: 2.75rem;
        border-radius: 0.31rem;
        border: none;
        outline: none;
        width: 100%;
        margin-bottom: 1rem;
      }
      button:nth-of-type(2) {
        background: transparent;
        border: 0.04rem solid #cfcfcf;
        color: #333333;
      }
    }
  }
}
</style>
