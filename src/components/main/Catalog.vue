<template>
    <div class="goods-list">
        <div v-show="goods.length" v-for="item in goods" :key="item.id_product" class="goods-item">
            <img src="../../assets/images/empty-image.svg" class="goods-item-empty" alt="product image">
            <h3>{{ item.product_name }}</h3>
            <p>{{ item.currency = '$' }}{{ item.price }}</p>
            <button :data-good-id=item.id_product @click="purchaseHandler(addItemToCart)">Купить</button>
        </div>
        <div class="goods-list-empty" v-show="!goods.length">
            <span>Нет товаров в наличии</span>
            <img src="../../assets/images/empty-catalog.svg" alt="Empty Catalog">
        </div>
    </div>
</template>

<script>
export default {
  props: {
    goods: {
      type: Array,
      default: () => []
    },
    addItemToCart: {
      type: Function
    }
  },
  methods: {
    purchaseHandler (purchasingFunction) {
      this.goods.forEach(good => {
        if (parseInt(event.target.dataset.goodId) === good.id_product) {
          purchasingFunction(good)
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/_variables.scss';
.goods-list {
  display: flex;
  justify-content: space-evenly;
  padding: 50px 0;

  .goods-item {
    width: 220px;
    height: 275px;
    padding: 160px 10px 25px 10px;
    border: 1px solid $defaultBeige;
    background-color: $defaultGreen;
    border-radius: 10px;
    color: $defaultBeige;
    transition: 0.3s;
    font-size: 13px;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    flex-direction: column;
    position: relative;

    & img {
      transition: 0.3s;
    }

    & button {
      background-color: $darkGreen;
      color: $defaultBeige;
      width: 50%;
      height: 25px;
      margin-top: 5px;
      border-radius: 25px;
      border: none;
      transition: 0.3s;
      box-shadow: 0 0 5px 2px #221;
      cursor: pointer;

      &:hover {
        box-shadow: 0 0 3px 1px #221;
      }

      &:active {
        box-shadow: none;
      }
    }

    &:hover {
      transform: scale(1.025);
      background-color: $darkGreen;

      & img {
        opacity: 0.5;
      }

      & button {
        background-color: $defaultGreen;
      }
    }

    &-empty {
      width: 75%;
      position: absolute;
      top: 10px;
    }
  }

  &-empty {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    & img {
      width: 150px;
      height: 150px;
    }
  }
}
</style>
