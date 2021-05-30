<template>
  <div id="app">
    <Header :filter="filterGoods" :toggle="toggleCart"/>
    <main class="main">
      <Catalog :goods="filteredGoods" :addItemToCart="addItemToCart"/>
    </main>
    <RequestError v-show="isVisibleErrorMessage"/>
    <Cart :cart="cart" :visibility="isVisibleCart" :toggle="toggleCart" />
    <footer class="footer"></footer>
  </div>
</template>

<script>
import Catalog from './components/main/Catalog'
import Header from './components/header/Header'
import Cart from './components/cartModal/Cart'
import RequestError from './components/RequestError'
const API_URL = 'https://raw.githubusercontent.com/GeekBrainsTutorial/online-store-api/master/responses'
export default {
  components: {
    Catalog,
    Header,
    Cart,
    RequestError
  },
  data: () => ({
    goods: [],
    filteredGoods: [],
    cart: [],
    isVisibleCart: false,
    isVisibleErrorMessage: false
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
        .catch((error) => {
          this.isVisibleErrorMessage = true
          console.log(`Error: ${error}`)
        })
    },
    filterGoods (value) {
      const regExp = new RegExp(value, 'i')
      this.filteredGoods = this.goods.filter(good => regExp.test(good.product_name))
    },
    toggleCart () {
      this.isVisibleCart = !this.isVisibleCart
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

.main {
  padding: 30px $wrapper;
  background-color: $defaultGreen;
}

.footer {
  padding: 40px $wrapper;
  background-color: $defaultPink;
}
</style>
