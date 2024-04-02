<script setup lang="ts">
import { computed, ref } from 'vue'

let currentColor = ref('bg-slate-500')

let productPrice = ref(0) //商品價格
let spendingThreshold = ref(0) //贈獎門檻
let voucherValue = ref(0) //贈獎金額
let previousSpending = ref(0) //先前消費總額
let extraCouponAmount = ref(0) //其他禮券
let extraDiscountAmount = ref(0) //其他折扣

let amountAfterDiscount = ref(0) //折後金額
let couponRemaining = ref(0) //餘券數量

//目前實際消費
const ChargeAmount = computed(
  () =>
    productPrice.value +
    previousSpending.value -
    extraCouponAmount.value -
    extraDiscountAmount.value
)
// 計算禮券數量，目前實際消費 / 折扣門檻
const couponAmount = computed(() => Math.floor(ChargeAmount.value / spendingThreshold.value))
// 下個贈獎門檻，(檻 * 券 + 1) + ( 贈 * 券 + 1) - 目前實際消費
const nextThreshold = computed(
  () =>
    (spendingThreshold.value + voucherValue.value) * (couponAmount.value + 1) - ChargeAmount.value
)

function calculate() {
  // 使用 for 來遞減商品金額
  for (let i = 0; i <= couponAmount.value; i++) {
    // 當商品折後價小於門檻金額時即停止迴圈
    if (ChargeAmount.value - i * voucherValue.value < i * spendingThreshold.value) {
      break
    } else {
      ChargeAmount.value - i * voucherValue.value
      console.log(ChargeAmount.value - i * voucherValue.value)
      console.log('可獲得禮券數:' + couponAmount.value)
      console.log('剩餘禮券數:' + (couponAmount.value - i))
      amountAfterDiscount.value =
        ChargeAmount.value - i * voucherValue.value - previousSpending.value
      couponRemaining.value = couponAmount.value - i
    }
    500030
  }
}
function changeColor(color) {
  currentColor.value = color
}
</script>

<template>
  <p class="text-sm">商品價格</p>
  <input type="number" v-model="productPrice" />
  <p>滿</p>
  <input type="number" v-model="spendingThreshold" />
  <p>送</p>
  <input type="number" v-model="voucherValue" />
  <p>先前消費金額</p>
  <input type="number" v-model="previousSpending" />
  <p>其他禮券使用</p>
  <input type="number" v-model="extraDiscountAmount" />
  <p>其他活動折扣</p>
  <input type="number" v-model="extraCouponAmount" />

  <button @click="calculate">試算折扣價</button>

  <div
    class="h-40 w-40 border bg-contain bg-center"
    style="background-image: url(/src/assets/images/avocado.jpg)"
  ></div>

  <div>折扣價: {{ amountAfterDiscount }}</div>
  <div>剩餘禮券數: {{ couponRemaining }}</div>
  <div>達下個門檻還剩餘: {{ nextThreshold }}</div>
  <div>可使用禮券數: {{ couponAmount }}</div>
  <div>目前實際消費（包含商品金額與其他消費，不含禮券及活動折扣）: {{ ChargeAmount }}</div>

  <div class="h-20 w-20 border" :class="currentColor"></div>
  <button @click="changeColor('bg-red-500')">change color</button>
</template>
