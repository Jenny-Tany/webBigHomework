<script setup>
import {ref} from 'vue'
import { useRouter } from 'vue-router'
import { orderListAPI } from "@/apis/order"
import { useUserStore } from "@/stores/user";

const checkInfo = {}  // 订单对象
const curAddress = {}  // 地址对象
const userStore = useUserStore();

const router = useRouter()
// 总计订单商品数量和价格
const allCount = ref()
const totalPrice = ref()

const orderListShow = ref({
    goodsName: [],
    count: [],
    price: []
})
// 获取用户id
const userId = userStore.userInfo;
// 获取订单列表
const orderLists = async () => {
    const res = await orderListAPI({ userId })
    console.log(111);
    console.log(res);
    res.forEach((item) => {
        item.orderDetail.forEach((detail) => {
            orderListShow.value.goodsName.push(detail.goodsName)
            orderListShow.value.count.push(detail.nums.split(',')[0].trim())
            orderListShow.value.price.push(detail.nums.split(',')[1].split('=')[1])
        })
    });
    allCount.value = orderListShow.value.count.reduce((total, num) => total + parseFloat(num), 0)
    totalPrice.value = orderListShow.value.price.reduce((total, num) => total + parseFloat(num), 0)
    
    console.log(`goodsName::${orderListShow.value.goodsName}`);
    console.log(`goodsCount::${orderListShow.value.count}`);
    console.log(`goodsPrice::${orderListShow.value.price}`);
    console.log(res[0].orderDetail[0].goodsName);
}
orderLists()
const submitOrder = () => {
    ElMessage({
  		message: '提交订单成功',
  		type: 'success',
  	})
    console.log(111);
    router.push({path: "/pay"})
}

</script>

<template>
  <div class="xtx-pay-checkout-page">
    <div class="container">
      <div class="wrapper">
        <!-- 商品信息 -->
        <h3 class="box-title">商品信息</h3>
        <div class="box-body">
          <table class="goods">
            <thead>
              <tr>
                <th width="520">商品信息</th>
                <th width="170">单价</th>
                <th width="170">数量</th>
                <!-- <th width="170">小计</th> -->
              </tr>
            </thead>
            <tbody  >
              <tr>
                <td>
                  <a href="javascript:;" class="info">
                    <div class="right">
                      <p v-for="i in orderListShow.goodsName" :key="i.id">{{ i }}</p>
                    </div>
                  </a>
                </td>
                <td>
                    <p v-for="p in orderListShow.price">&yen;{{ p }}</p>
                </td>
                <td>
                    <p  v-for="cnt in orderListShow.count">{{ cnt }}</p>
                </td>

                <!-- <td>&yen;{{ i.totalPrice }}</td> -->
                <!-- <td>&yen;{{ i.totalPayPrice }}</td> -->
              </tr>
            </tbody>
          </table>
        </div>
        <!-- 配送时间 -->
        <h3 class="box-title">配送时间</h3>
        <div class="box-body">
          <a class="my-btn active" href="javascript:;">不限送货时间：周一至周日</a>
          <a class="my-btn" href="javascript:;">工作日送货：周一至周五</a>
          <a class="my-btn" href="javascript:;">双休日、假日送货：周六至周日</a>
        </div>
        <!-- 支付方式 -->
        <h3 class="box-title">支付方式</h3>
        <div class="box-body">
          <a class="my-btn active" href="javascript:;">在线支付</a>
          <a class="my-btn" href="javascript:;">货到付款</a>
          <span style="color:#999">货到付款需付5元手续费</span>
        </div>
        <!-- 金额明细 -->
        <h3 class="box-title">金额明细</h3>
        <div class="box-body">
          <div class="total">
            <dl>
              <dt>商品件数：</dt>
              <dd>{{ allCount }}件</dd>
            </dl>
            <dl>
              <dt>商品总价：</dt>
              <dd>¥{{ totalPrice.toFixed(2) }}</dd>
            </dl>
            <dl>
              <dt>应付总额：</dt>
              <dd class="price">¥{{ totalPrice.toFixed(2) }}</dd>
            </dl>
          </div>
        </div>
        <!-- 提交订单 -->
        <div class="submit">
          <el-button type="primary" size="large" 
          :plain="true"
          @click="submitOrder()">提交订单</el-button>
        </div>
      </div>
    </div>
  </div>
  <!-- 切换地址 -->
  <!-- 添加地址 -->
</template>

<style scoped lang="scss">
$xtxColor: #45c191;
$priceColor: #67b587;
.xtx-pay-checkout-page {
  margin-top: 20px;

  .wrapper {
    background: #fff;
    padding: 0 20px;

    .box-title {
      font-size: 16px;
      font-weight: normal;
      padding-left: 10px;
      line-height: 70px;
      border-bottom: 1px solid #f5f5f5;
    }

    .box-body {
      padding: 20px 0;
    }
  }
}

.address {
  border: 1px solid #f5f5f5;
  display: flex;
  align-items: center;

  .text {
    flex: 1;
    min-height: 90px;
    display: flex;
    align-items: center;

    .none {
      line-height: 90px;
      color: #999;
      text-align: center;
      width: 100%;
    }

    >ul {
      flex: 1;
      padding: 20px;

      li {
        line-height: 30px;

        span {
          color: #999;
          margin-right: 5px;

          >i {
            width: 0.5em;
            display: inline-block;
          }
        }
      }
    }

    >a {
      color: $xtxColor;
      width: 160px;
      text-align: center;
      height: 90px;
      line-height: 90px;
      border-right: 1px solid #f5f5f5;
    }
  }

  .action {
    width: 420px;
    text-align: center;

    .btn {
      width: 140px;
      height: 46px;
      line-height: 44px;
      font-size: 14px;

      &:first-child {
        margin-right: 10px;
      }
    }
  }
}

.goods {
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;

  .info {
    display: flex;
    text-align: left;

    img {
      width: 70px;
      height: 70px;
      margin-right: 20px;
    }

    .right {
      line-height: 24px;

      p {
        &:last-child {
          color: #999;
        }
      }
    }
  }

  tr {
    th {
      background: #f5f5f5;
      font-weight: normal;
    }

    td,
    th {
      text-align: center;
      padding: 20px;
      border-bottom: 1px solid #f5f5f5;

      &:first-child {
        border-left: 1px solid #f5f5f5;
      }

      &:last-child {
        border-right: 1px solid #f5f5f5;
      }
    }
  }
}

.my-btn {
  width: 228px;
  height: 50px;
  border: 1px solid #e4e4e4;
  text-align: center;
  line-height: 48px;
  margin-right: 25px;
  color: #666666;
  display: inline-block;

  &.active,
  &:hover {
    border-color: $xtxColor;
  }
}

.total {
  dl {
    display: flex;
    justify-content: flex-end;
    line-height: 50px;

    dt {
      i {
        display: inline-block;
        width: 2em;
      }
    }

    dd {
      width: 240px;
      text-align: right;
      padding-right: 70px;

      &.price {
        font-size: 20px;
        color: $priceColor;
      }
    }
  }
}

.submit {
  text-align: right;
  padding: 60px;
  border-top: 1px solid #f5f5f5;
}

.addressWrapper {
  max-height: 500px;
  overflow-y: auto;
}

.text {
  flex: 1;
  min-height: 90px;
  display: flex;
  align-items: center;

  &.item {
    border: 1px solid #f5f5f5;
    margin-bottom: 10px;
    cursor: pointer;

    &.active,
    &:hover {
      border-color: $xtxColor;
      background: lighten($xtxColor, 50%);
    }

    >ul {
      padding: 10px;
      font-size: 14px;
      line-height: 30px;
    }
  }
}
</style>