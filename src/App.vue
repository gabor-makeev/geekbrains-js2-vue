<template>
  <div id="app">
    <header class="header">
      <a href="#" class="header-logo">Это интернет-магазин...</a>
      <div class="search">
        <input type="text" class="search-field" v-model="searchField">
        <button class="search-button" type="button" @click="filterGoods(searchField)">Go</button>
      </div>
      <button class="cart-button" type="button" @click="toggleCart()">Корзина</button>
    </header>
    <main class="main">
      <div class="goods-list">
        <div v-show="filteredGoods" v-for="item in filteredGoods" :key="item.id_product" class="goods-item">
          <img src="./assets/images/empty-image.svg" class="goods-item-empty" alt="product image">
          <h3>{{ item.product_name }}</h3>
          <p>{{ item.currency = '$' }}{{ item.price }}</p>
          <button :data-good-id=item.id_product @click="purchaseHandler()">Купить</button>
        </div>
        <div class="goods-list-empty" v-show="!filteredGoods.length">
          <span>Нет товаров в наличии</span>
          <img src="./assets/images/empty-catalog.svg" alt="Empty Catalog">
        </div>
      </div>
    </main>
    <section class="cart-list" v-show="isVisibleCart">
      <div class="cart-list-box">
        <h2 class="cart-list-title">Корзина:</h2>
        <div class="cart-list-array">
          <div v-for="item in cart" :key="item.id_product" class="goods-item">
            <img src="./assets/images/empty-image.svg" class="goods-item-empty" alt="product image">
            <h3>{{ item.product_name }}</h3>
            <p>Общая стоимость: {{ item.currency = '$' }}{{ item.price * item.quantity }}</p>
            <p>Количество: {{ item.quantity }}</p>
            <button :data-good-id=item.id_product @click="removeHandler()">Удалить</button>
          </div>
        </div>
      </div>
      <svg aria-hidden="true" focusable="false" data-prefix="fas" data-icon="times" class="svg-inline--fa fa-times fa-w-11 cart-list-svg" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 352 512" @click="toggleCart()"><path d="M242.72 256l100.07-100.07c12.28-12.28 12.28-32.19 0-44.48l-22.24-22.24c-12.28-12.28-32.19-12.28-44.48 0L176 189.28 75.93 89.21c-12.28-12.28-32.19-12.28-44.48 0L9.21 111.45c-12.28 12.28-12.28 32.19 0 44.48L109.28 256 9.21 356.07c-12.28 12.28-12.28 32.19 0 44.48l22.24 22.24c12.28 12.28 32.2 12.28 44.48 0L176 322.72l100.07 100.07c12.28 12.28 32.2 12.28 44.48 0l22.24-22.24c12.28-12.28 12.28-32.19 0-44.48L242.72 256z"></path></svg>
    </section>
    <footer class="footer"></footer>
  </div>
</template>

<script>
const API_URL = 'https://raw.githubusercontent.com/GeekBrainsTutorial/online-store-api/master/responses'
export default {
  data: () => ({
    goods: [],
    filteredGoods: [],
    cart: [],
    searchField: '',
    isVisibleCart: false
  }),
  mounted () {
    this.makeGETRequest(`${API_URL}/catalogData.json`)
  },
  methods: {
    makeGETRequest (url) {
      fetch(url)
        .then((data) => data.json())
        .then((data) => {
          this.goods = data
          this.filteredGoods = data
        })
    },
    filterGoods (value) {
      const regExp = new RegExp(value, 'i')
      this.filteredGoods = this.goods.filter(good => regExp.test(good.product_name))
    },
    toggleCart () {
      this.isVisibleCart = !this.isVisibleCart
    },
    purchaseHandler () {
      this.filteredGoods.forEach(good => {
        if (parseInt(event.target.dataset.goodId) === good.id_product) {
          this.addItemToCart(good)
        }
      })
    },
    pushToCart (item) {
      this.cart.push({ ...item, quantity: 1 })
    },
    increaseQuantity (item) {
      item.quantity++
    },
    isInCart (unknownItem, cartItem) {
      return unknownItem.product_name === cartItem.product_name
    },
    addItemToCart (item) {
      let inCart = false
      if (this.cart.length) {
        this.cart.forEach(good => {
          if (this.isInCart(item, good)) {
            this.increaseQuantity(good)
            inCart = true
          }
        })
      } else {
        this.pushToCart(item)
        return
      }
      if (!inCart) {
        this.pushToCart(item)
      }
    },
    removeHandler () {
      this.cart.forEach(good => {
        if (parseInt(event.target.dataset.goodId) === good.id_product) {
          this.removeFromCart(good)
        }
      })
    },
    removeFromCart (item) {
      for (let idx = 0; idx < this.cart.length; idx++) {
        if (this.cart[idx].product_name === item.product_name) {
          if (this.cart[idx].quantity > 1) {
            this.cart[idx].quantity--
          } else {
            this.cart.splice(idx, 1)
          }
        }
      }
    }
  },
  watch: {
    searchField () {
      this.filterGoods(this.searchField)
    }
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@100;200;300;400;500;600;700&display=swap');
@import '@/assets/_variables.scss';

* {
  padding: 0;
  margin: 0;
  font-family: 'Roboto Mono', monospace;
  box-sizing: border-box;
}

#app {
  display: grid;
  grid-template-rows: min-content 1fr min-content;
  min-height: 100vh;
  position: relative;
}

.header {
  padding: 50px $wrapper;
  background-color: $defaultBeige;
  display: flex;
  justify-content: space-between;
  align-items: center;

  &-logo {
    font-size: 25px;
    text-decoration: none;
    color: $defaultGreen;
    display: flex;
    align-items: center;
    transition: 0.3s;

    &:hover {
      color: #a7ad93;
    }
  }

  .search {
    display: flex;
    gap: 15px;

    &-field {
      background-color: $defaultBeige;
      border-color: $defaultGreen;
      color: $defaultGreen;
      border-radius: 25px;
      padding: 0 15px 0 15px;
      width: 200px;
      height: 40px;
      outline: none;
    }

    &-button {
      outline: none;
      border: 1px solid $defaultGreen;
      border-radius: 25px;
      cursor: pointer;
      width: 40px;
      height: 40px;
      background-color: $darkGreen;
      color: $defaultBeige;
      transition: 0.3s;

      &:hover {
        transform: scale(1.05);
        background-color: $defaultBeige;
        border-color: $defaultGreen;
        color: $defaultGreen;
      }

      &:active {
        background-color: $defaultGreen;
        color: $defaultBeige;
      }
    }
  }
}

.cart-button {
  padding: 10px 30px;
  border: 1px solid #cec795;
  border-radius: 10px;
  background-color: $defaultGreen;
  color: $defaultBeige;
  font-size: 16px;
  cursor: pointer;
  transition: 0.3s;

  &:hover {
    transform: scale(1.05);
    background-color: $defaultBeige;
    border-color: $defaultGreen;
    color: $defaultGreen;
  }

  &:active {
    background-color: $defaultGreen;
    color: $defaultBeige;
  }
}

.main {
  padding: 30px $wrapper;
  background-color: $defaultGreen;
}

.goods-list,
.cart-list-array {
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

.cart-list {
  background-color: rgba(122, 122, 122, 0.5);
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  position: absolute;
  display: flex;
  align-self: center;
  justify-self: center;

  &-box {
    top: 0; right: 0; bottom: 0; left: 0;
    display: flex;
    align-self: center;
    flex-direction: column;
    margin: 0 auto;
    width: 50%;
    min-height: 460px;
    background-color: #faf2dab9;
    border-radius: 25px;
    -webkit-box-shadow: 0 0 5px 1px #363636;
    box-shadow: 0 0 5px 1px #363636;
  }

  &-title {
    margin: 20px 0 0 25px;
  }

  &-array {
    flex-wrap: wrap;
    gap: 20px;
  }

  &-svg {
    position: absolute;
    width: 40px;
    height: 40px;
    cursor: pointer;
    transition: 0.3s;
    right: 50px;
    top: 50px;

    &:hover {
      fill: rgb(19, 19, 19);
      transform: scale(1.1);
    }
  }
}

.footer {
  padding: 40px $wrapper;
  background-color: $defaultPink;
}
</style>
