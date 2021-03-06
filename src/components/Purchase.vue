<template>
  <div id="purchase">
    <div class="purchase__title-wrapper">
      <h2 class="purchase__title">二步驟 <br> 打造專屬殼</h2>
    </div>
    <!-- 手機殼顯示區 -->
    <div class="display__area">
      <section class="purchase__preview">
        <div class="purchase__preview-images-wrapper">
          <img :src="previewImageSrc.phone" alt="" class="purchase__preview-image main">
          <img :src="previewImageSrc.frame" alt="" class="purchase__preview-image sub case">
          <img
            :src="previewImageSrc.rim"
            alt=""
            :class="[
              'purchase__preview-image',
              'sub',
              'rim',
              previewMode === 'rim' ? 'purchase__preview-image--forward' : 'purchase__preview-image--backward'
            ]"
          >
          <img :src="previewImageSrc.button" alt="" class="purchase__preview-image sub button">
          <img
            :src="previewImageSrc.backPlate"
            alt=""
            :class="[
              'purchase__preview-image',
              'sub',
              'backplate',
              previewMode === 'backPlate' ? 'purchase__preview-image--forward' : 'purchase__preview-image--backward'
            ]"
          >
          </div>
          <div class="purchase__selector-wrapper phone">
            <div class="color-container">
              <p class="color-picker-title">裝置<br>顏色</p>
              <div class="normal color-picker color-picker--vertical">
                <div id="apple_graphite" class="color-picker--vertical__option active">
                  <div class="dot apple_graphite"></div>
                </div>
                <div id="silver" class="color-picker--vertical__option">
                  <div class="dot silver"></div>
                </div>
                <div id="gold" class="color-picker--vertical__option">
                  <div class="dot gold"></div>
                </div>
                <div id="pacific_blue" class="color-picker--vertical__option">
                  <div class="dot pacific_blue"></div>
                </div>
              </div>
            </div>
          </div>
          <div class="purchase__backplate-switch-wrapper">
            <div class="backplate-switch light">
              <section class="backplate-switch__common">
                <div class="backplate-switch__option">
                  <div class="backplate-switch__checkbox backplate-switch__checkbox--active"></div>
                  <input
                    v-model="previewMode"
                    id="backPlate-radio"
                    class="backplate-switch__option__radio"
                    value="backPlate"
                    type="radio"
                    name="preview-mode"
                  >
                  <label class="backplate-switch__mode-name" for="backPlate-radio">背板模式</label>
                </div>
                <div class="backplate-switch__option">
                  <div class="backplate-switch__checkbox"></div>
                  <input
                    v-model="previewMode"
                    id="rim-radio"
                    class="backplate-switch__option__radio"
                    value="rim"
                    type="radio"
                    name="preview-mode"
                  >
                  <label class="backplate-switch__mode-name" for="rim-radio">邊框模式</label>
                </div>
              </section>
            </div>
          </div>
        </section>
    </div>
    <!-- 型號/顏色顯示區 -->
    <div class="selection__area">
      <div class="selection__area__top">
        <div class="device__selector">
          <select name="" id="device" @change="setProduct('init')" v-model="selectedDevice">
            <option value="iphone-12-pro-max">iPhone 12 Pro Max</option>
            <option value="iphone-12-pro">iPhone 12 Pro</option>
            <option value="iphone-12">iPhone 12</option>
          </select>
        </div>

        <div class="outward">
          <div class="option__wrapper">
            <div class="option__block" @click="isShowFrameRadio = !isShowFrameRadio">
              <p class="option__title">手機殼顏色</p>
              <div class="icon"></div>
            </div>
            <div :class="['frame__radio', { 'frame__radio--active': isShowFrameRadio }]">
              <div
                class="frame__radio__container"
                v-for="(variant, index) in frames"
                :key="variant + index"
              >
                <input
                  @click="setProduct('frame', index)"
                  :class="['frame__radio__button', selectedFrameColor.split(' ')[0]]"
                  name="frame"
                  type="radio"
                />
              </div>
            </div>
          </div>
          <div class="option__wrapper">
            <div class="option__block" @click="isShowBackPlateSelector = !isShowBackPlateSelector">
              <p class="option__title">背板樣式</p>
              <div class="icon"></div>
            </div>
            <div :class="['back-plate', { 'back-plate--active': isShowBackPlateSelector }]">
              <div
                @click="setProduct('tarnsparentBackPlate')"
                :class="['back-plate__container', { 'back-plate__container--checked' : backPlateIndex === 0}]"
              >
                <img
                  class="back-plate__image"
                  :src="isFetched ? productsInfo[selectedDevice].tarnsparentBackPlate.variants[0].image : ''"
                  alt="透明背板"
                >
              </div>
              <div
                @click="setProduct('backPlate')"
                :class="['back-plate__container', { 'back-plate__container--checked' : backPlateIndex === 1}]"
              >
                <img
                  class="back-plate__image"
                  :src="isFetched ? productsInfo[selectedDevice].backPlate.variants[0].image : ''"
                  alt="背板"
                >
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="checkout">
        <div class="checkout__detail">
          <p class="checkout__detail__info">產品包含:</p>
          <p class="checkout__detail__info">手機殼外框 ({{ selectedFrameColor }}) x 1</p>
          <p class="checkout__detail__info">按鍵 ({{ selectedFrameColor }}) x 3</p>
          <p class="checkout__detail__info">背板 ({{ selectedBackPlateColor }}) x 1</p>
          <p class="checkout__detail__info">飾條 ({{ selectedFrameColor }}) x 1</p>
        </div>
        <div class="checkout__button__block">
          <button class="checkout__btn" @click="addToCart">加入購物車</button>
          <p class="price">${{ price }} TWD</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  components: {
  },
  props: {
  },
  data () {
    return {
      devices: ['iphone-12-pro-max', 'iphone-12-pro', 'iphone-12'],
      productParts:
      {
        phone: 'type-mod-nx-phone-45degree', // 手機
        frame: 'type-mod-nx-frame-45degree', // 外框
        rim: 'rim', // 飾條
        button: 'button', // 按鈕
        backPlate: 'customized-backprint', // 背板
        tarnsparentBackPlate: 'category-mod-nx-backplate-basic' // 透明背板
      },
      selectedDevice: 'iphone-12-pro-max', // 選擇的機種
      backPlateIndex: 0, // 因為背板直接寫死，所以存選擇第幾個
      previewMode: 'backPlate',
      isShowFrameRadio: false,
      isShowBackPlateSelector: false,
      productsInfo: {}, // 處理過後的產品資料
      cartItems: [],
      product: {}, // 目前預覽的產品
      isFetched: false // 資料是否載完
    }
  },
  computed: {
    frames () {
      if (this.isFetched) {
        return this.productsInfo[this.selectedDevice].frame.variants
      } else {
        return []
      }
    },
    previewImageSrc () {
      if (this.isFetched) {
        return {
          phone: this.product.phone.image,
          frame: this.product.frame.image,
          rim: this.product.rim.image,
          button: this.product.button.image,
          backPlate: this.product.backPlate.image
        }
      } else {
        return {
          phone: 'https://cdn.shopify.com/s/files/1/0740/2335/products/phone45-12-Pro_AppleGraphite_d04b461d-15c3-4e43-9b43-a828eaa4478c.png?v1603352312',
          frame: 'https://cdn.shopify.com/s/files/1/0740/2335/products/Black-Bumper_87d2c086-7e6a-4f40-93d9-33828a249bbe.png?v=1597911746',
          rim: 'https://cdn.shopify.com/s/files/1/0740/2335/products/Black-Rim_2e73b29c-7262-463d-b047-2f91a790068a.png?v=1597911791',
          button: 'https://cdn.shopify.com/s/files/1/0740/2335/products/Black-Button_23534b09-c1aa-4ab4-8174-ded2bc09069e.png?v=1597911773',
          backPlate: 'https://cdn.shopify.com/s/files/1/0740/2335/products/minisite-NPB0118526L_b4665252-c418-480f-9ca3-b84fb860ca2c.png?v=1603868182'
        }
      }
    },
    selectedFrameColor () {
      return this.isFetched ? this.product.frame.optionsWithKey.Color : ''
    },
    selectedBackPlateColor () {
      if (this.isFetched) {
        // db.json 有Color也有color
        return this.product.backPlate.optionsWithKey.Color ? this.product.backPlate.optionsWithKey.Color : this.product.backPlate.optionsWithKey.color
      } else {
        return ''
      }
    },
    price () {
      return this.isFetched ? this.product.backPlate.price : ''
    }
  },
  created () {
    this.fetchProductData()
    this.selectedDevice = this.devices[0]
  },
  methods: {
    fetchProductData () {
      axios.get('http://localhost:3000/mod-nx')
        .then(response => {
          // 存成 {
          //  機種A: {機殼: {...}, 按鈕: {...}...},
          //  機種B: {機殼: {...}, 按鈕: {...}...}
          // }
          response.data.data.forEach(deviceInfo => {
            this.devices.forEach(device => {
              if (deviceInfo.tags.includes(`device-${device}`)) {
                for (const [part, tag] of Object.entries(this.productParts)) {
                  if (deviceInfo.tags.includes(tag)) {
                    if (!this.productsInfo[device]) {
                      this.productsInfo[device] = {}
                    }
                    this.productsInfo[device][part] = deviceInfo
                  }
                }
              }
            })
          })

          this.setProduct('init')
          this.isFetched = true
        })
    },

    addToCart () {
      this.cartItems.push(this.product)
      console.log(this.cartItems)
    },

    setProduct (part, index) {
      const product = Object.assign({}, this.product)
      if (part === 'init') { // 第一次進入或切換機種
        ['phone', 'frame', 'rim', 'button'].forEach(key => {
          product[key] = this.productsInfo[this.selectedDevice][key].variants[0]
        })
        product.backPlate = this.productsInfo[this.selectedDevice].tarnsparentBackPlate.variants[0] // 預設透明背板
      } else if (part === 'frame') { // 更換手機殼顏色
        ['frame', 'rim', 'button'].forEach(key => {
          product[key] = this.productsInfo[this.selectedDevice][key].variants[index]
        })
      } else if (part === 'tarnsparentBackPlate') { // 選擇透明背板
        product.backPlate = this.productsInfo[this.selectedDevice].tarnsparentBackPlate.variants[0]
        this.backPlateIndex = 0
      } else { // 選擇普通背板
        product.backPlate = this.productsInfo[this.selectedDevice].backPlate.variants[0]
        this.backPlateIndex = 1
      }
      this.product = product
    }
  }
}
</script>

<style lang="sass" scoped>
@font-face
  font-family: MarkPro-Bold
  src: url('https://cdn.shopify.com/s/files/1/0274/8717/files/MarkPro-Bold.otf?12401685184872946130')

@font-face
  font-family: NotoSansCJKtc-Bold
  src: url('https://cdn.shopify.com/s/files/1/0274/8717/files/NotoSansCJKtc-Bold.otf?12610878586689504297')

@font-face
  font-family: MarkPro
  src: url('https://cdn.shopify.com/s/files/1/0274/8717/files/MarkPro.otf?4269257120200746974')

@font-face
    font-family: NotoSansCJKtc-Regular
    src: url(https://cdn.shopify.com/s/files/1/0274/8717/files/NotoSansCJKtc-Regular.otf?4935245772218057441)

#purchase
  position: relative
  width: 100%
  height: 100%
  max-width: 1440px
  display: flex
  padding: 120px 60px
  align-items: flex-start
  justify-content: space-between
  box-sizing: border-box

.purchase__title-wrapper
  width: 25%
  max-width: 220px

.display__area
  height: 100%

.selection__area
  z-index: 10
  display: flex
  flex-direction: column
  justify-content: space-between
  align-self: stretch
  margin-left: 70px
  width: 35%
  max-width: 260px

  .device__selector
    margin-bottom: 25px

  .checkout

    &__detail
      font-family: 'MarkPro', 'NotoSansCJKtc-Regular'

      &__info
        margin: 15px 0
        padding: 0
        line-height: 14px
        font-size: 14px
        text-align: left

    &__button__block
      display: flex
      justify-content: space-between
      align-items: center
      font-family: 'MarkPro-Bold', 'NotoSansCJKtc-Bold'

      .price
        font-size: 14px
        text-algin: right

    &__btn
      display: flex
      justify-content: center
      align-items: center
      width: 100%
      height: 40px
      max-width: 180px
      color: #FFF
      // font-family: 'MarkPro-Bold', 'NotoSansCJKtc-Bold'
      box-shadow: none
      border: none
      border-radius: 20.5px
      background-color: #2D2D2D
      transition: all .3s ease
      white-space: inherit

  .outward
    border-top: 2px solid #D8D8D8

  .option__wrapper
    border-bottom: 1px solid #D8D8D8

    .option__block
      position: relative
      display: flex
      justify-content: flex-start
      align-items: center
      padding: 20px 0
      cursor: pointer

      .option__title
        font-size: 14px
        font-family: 'MarkPro', 'NotoSansCJKtc-Regular'
        color: #2D2D2D

      .icon
        position: absolute
        right: 0
        top: 50%
        width: 16px
        height: 16px
        transform: translateY(-50%)

        &::before
          content: ''
          position: absolute
          left: 0
          width: 1px
          height: 16px
          background-color: #D8D8D8
          transform: rotate(90deg)
          transition: all .3s ease

        &::after
          content: ''
          position: absolute
          left: 0
          width: 1px
          height: 16px
          background-color: #D8D8D8
          transition: all .3s ease

      p
        margin: 0
        padding: 0

    .frame__radio
      display: none
      flex-wrap: wrap

      &--active
        display: flex

      &__container
        display: flex
        justify-content: center
        align-items: center
        margin: 5px
        padding: 2px
        width: 15px
        height: 15px
        border: 1px solid #fff
        border-radius: 50%

      &__button
        margin: 0
        padding: 2px
        width: 100%
        height: 100%
        border-radius: 50%
        background-color: #777
        outline: none
        cursor: pointer
        appearance: none

        &:checked:after
          content: ''
          display: block
          width: 20px
          height: 20px
          border: 1px solid #000
          border-radius: 50%
          transform: translate(-25%, -25%)

        &.Mint
          background-color: #98ff98
        &.Navy
          background-color: navy

    .back-plate
      display: none
      padding: 10px 0

      &--active
        display: flex

      &__container
        display: flex
        justify-content: center
        align-items: center
        box-sizing: border-box
        margin: 0 10px
        width: 50px
        height: 50px
        border-radius: 10px
        background-color: #fff
        cursor: pointer

        &--checked
          border: 3px solid #000

      .back-plate__image
        width: 80%
        height: 80%

.purchase__preview
  position: relative
  margin: 0 auto
  width: 100%
  max-width: 650px
  height: 100%
  &-images-wrapper
    position: relative
    height: 80%
  &-image
    &.main
      position: relative
      height: 100%
      z-index: 2
    &.sub
      position: absolute
      height: 100%
      top: 0
      left: 0
    &--backward
      z-index: 1
      transition: all .3s
      transform: translate(12%,-10%)
    &--forward
      z-index: 2
      transition: all .3s
    &.case
      z-index: 3

.purchase__selector-wrapper
  &.phone
    position: absolute
    top: 50%
    left: 25%
    transform: translateY(-50%)
    z-index: 3

.purchase__backplate-switch-wrapper
  position: absolute
  top: 50px
  right: 0
  z-index: 3

  .backplate-switch__option
    display: flex
    justify-content: center
    align-items: center
    padding: 20px 0
    cursor: pointer

    .backplate-switch__option__radio,
    .backplate-switch__mode-name
      cursor: pointer

.color-picker-title
  position: absolute
  top: 0
  right: 0
  margin: 0
  font-size: 12px
  color: #000
  line-height: normal
  text-transform: uppercase
  transform: translateY(-120%)
  opacity: .2

.color-picker--vertical
  display: flex
  align-items: center
  justify-content: flex-start
  flex-direction: column
  --desc-text-width: 150px
  --desc-dash-width: 16px
  --desc-dash-margin: 13px

  &.normal
    --border-color: #d5d5d5
    --hover-border: #d5d5d5
    --dash-color: #d8d8d8
    --font-color: #2d2d2d

  &__option
    position: relative
    width: 29px
    height: 29px
    border: 1px solid transparent
    border-radius: 50%
    z-index: 200
    transition: all .3s ease
    margin-bottom: 18px
    display: flex
    align-items: center
    justify-content: center
    &:hover
      border: 1px solid var(--hover-border)
    .active, .dot
      width: 23px
      height: 23px
    .dot
      border: 1px solid var(--border-color)
      border-radius: 50%
      cursor: pointer

.apple_graphite
  background-color: #686763

.silver
  background-color: #c9c8c9

.gold
  background-color: #fadcc2

.pacific_blue
  background-color: #43616c
</style>
