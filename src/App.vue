<template>
  <div id="app">
    <h1>VueJS Currency Exchange App</h1>
    <div class="container">
      <div class="container-one">
        <select name="first-currency" id="first-currency" v-model="currency_one" @click="fetchData()">
          <option v-for="currency in Object.keys(data.conversion_rates)" :value="currency">{{ currency }}</option>
        </select>
        <input type="number" name="input-one" id="input-one" v-model="amountOne" @input="fetchData()"/>
      </div>
      <div class="container-two">
        <button @click="switchValue()">Switch</button>
        <h4 id="baseValue"> 1 {{currency_one }} =  {{ rate }} {{ currency_two }}</h4>
      </div>
      <div class="container-three">
        <select name="second-currency" id="second-currency" v-model="currency_two" @click="fetchData()">
          <option v-for="currency in Object.keys(data.conversion_rates)" :value="currency">{{ currency }}</option>
        </select>
        <input type="number" id="amount-two" placeholder="0" disabled v-model="amountTwo"/>
      </div>
      <div class="container-four">
        <h4 id="lastlyUpdated">Lastly Updated {{data.time_last_update_utc}}</h4>
      </div>
    </div>
    <div>

    </div>
    <div class="actual_rate">
      <ul>
        <li v-for="item in initStateArr">{{ item }}</li>
      </ul>
    </div>

    <div>
     <button class="button" @click="showModal = true">Show Modal</button>
     <transition name="fade" appear>
      <div class="modal-overlay" v-if="showModal"></div>
     </transition>
     <transition name="slide">
      <div class="modal" v-if="showModal">
        <h1>Lorem Ipsum</h1>
        <input type="search" placeholder="Search..." class="search" v-model="search">
        <div class="scroll">
          <ul style="cursor : pointer;">
          <li v-for="item in searchHandler" @click="pushValueArr(item)">{{ item }}</li>
        </ul>
        </div>
        <button class="button" @click="showModal = false">Hide Modal</button>
      </div>
     </transition>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      data : [],
      currency_one : "USD",
      currency_two : "EUR",
      rate : "",
      amountOne : 1,
      amountTwo : 0,
      showModal : false,
      search : '',
      initStateArr : [],
    }
  },
  methods : {
    fetchData (){
      fetch(`https://v6.exchangerate-api.com/v6/61e038886d14d1650464cf83/latest/${this.currency_one}`)
      // fetch(`https://exchange-rates.abstractapi.com/v1/live/?api_key=f836dcabd80e49e798405e7fd89a709f&base=${this.currency_one}`)
      .then(res => res.json())
      .then(data => {
        this.data = data;
        this.rate = data.conversion_rates[this.currency_two];
        this.amountTwo = this.amountOne * this.rate.toFixed(2);
      });
    },
    switchValue(){
      const temp = this.currency_one;
      this.currency_one = this.currency_two;
      this.currency_two = temp;
      this.fetchData();
    },
    pushValueArr(item){
      if(!this.initStateArr.includes(item)){
        this.initStateArr.push(item);
      }
      }
  },
  computed :{
    searchHandler(){
      return Object.keys(this.data.conversion_rates).filter(el => {
        return el.includes(this.search.toUpperCase());
      })
    },
  },
  mounted(){
    this.fetchData();
  }
};
</script>

<style>
html {
  background: #f4f4f4;
}
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  align-content: center;
  width: 100%;
  height: 100%;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

h1{
  color: #5fbaaf;
}

.container {
  width: 50%;
  height: 50%;
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  text-align: center;
}

.container-two {
  display: flex;
  align-items: center;
  justify-content: space-evenly;
  width: 50%;
}
.container-two button {
  padding: 5px;
  font-size: 18px;
  background-color: #5fbaaf;
  color: #fff;
  width: 30%;
  height: 10%;
  border: none;
  outline: none;
  cursor: pointer;
}

select{
  padding: 5px;
  margin: 5px;
  border: 1px solid rgba(0,0,0,0.5);
  outline: none;
}

input {
  padding: 5px;
  margin: 5px;
  border: 1px solid rgba(0,0,0,0.5);
  outline: none;
  font-size: 18px;
}

#lastlyUpdated {
  font-weight: 500;
}
#baseValue {
  font-weight: 500;
}
.app{
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  width: 100vh;
  overflow-x: hidden;
}

.button {
  appearance: none;
  outline: none;
  border: none;
  background: none;
  cursor: pointer;

  display: inline-block;
  padding: 15px 25px;
  background-image: linear-gradient(to right, #CC2E5D, #FF5858);
  border-radius: 8px;
  color: #fff;
  font-size: 18px;
  font-weight: 700;
  box-shadow: 3px 3px  rgba(0,0,0,0.4);
  transition: 0.4s ease-out;
}
.button:hover{
  box-shadow: 6px 6px rgba(0,0,0,0.6);
}

.modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 98;
  background-color: rgba(0,0,0,0.3);
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}


.slide-enter-active,
.slide-leave-active {
  transition: opacity 0.5s;
}
.slide-enter,
.slide-leave-to {
  opacity: 0;
}
.modal{
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
  z-index: 99;
  width: 100%;
  max-width: 400px;
  background-color: #fff;
  border-radius: 16px;
  padding: 25px;
}
h1 {
  color: #222;
  font-size: 32px;
  font-weight: 900;
  margin-bottom: 15px;
}
p{
  color:#666;
  font-size: 18px;
  font-weight: 400;
  margin-bottom: 15px;
}
.scroll {
  height: 150px;
  overflow-x: scroll;
}
</style>
