<template>
  <header>
    <div class="container">
      <h1 id="header-text">Welcome to BurgerHeaven Online!</h1>
      <img id="header-background" src="https://thatsup.se/content/img/place/t/u/tugg-burgers-uppsala-0.jpg">
    </div>

  </header>
  <main>
    <section id="burgers">
      <h3>Select burger</h3>
      <p>This is where you execute burger selection</p>
      <div class="wrapper">
        <Burger v-for="burger in burgers" v-bind:burger="burger"
                v-bind:key="burger.name" v-on:orderedBurger="addToOrder($event)"/>

        <!--
        <div class="grilled">
          <figure>
            <h2>The Fire Burger</h2>
            <img class="bild-burger" src="grillad_burgare.jpeg" alt="Span" title="Grilled Burger">
            <ul>
              <li>Kött</li>
              <li>Bröd</li>
              <li>Tomat</li>
            </ul>
          </figure>
        </div>
        <div class="nacho">
          <figure>
            <h2>The Nacho Burger</h2>
            <img class="bild-burger" src="nacho_burger.jpeg" alt="Span" title="Nacho Burger">
            <ul>
              <li>Kött</li>
              <li>Bröd</li>
              <li>Nachos</li>
              <li>Jalapenos</li>
            </ul>
          </figure>

        </div>
        <div class="vegan">
          <figure>
            <h2>The Vegan Burger</h2>
            <img class="bild-burger" src="vegan_burger.jpeg" alt="Span" title="Vegan Burger">
            <ul>
              <li>Något veganskt i dunno</li>
              <li>vegansk Bröd</li>
              <li>vegansk Tomat</li>
            </ul>
          </figure>
        </div>
       -->
      </div>
    </section>
    <div id="cont" >
      <div id="map" v-on:click="setLocation" >
        <div v-bind:style="{left: this.location.x + 'px',
                       top: this.location.y + 'px'}">
          T
        </div>
      </div>
    </div>
    <section id="contact">
      <h3>Customer information</h3>
      <form>
        <p>
          <input type="radio" id="male" v-model="drone" value="male"
                 checked="checked">
          <label for="male">Male</label><br>

          <input type="radio" id="female" v-model="drone" value="female">
          <label for="female">Female</label><br>

          <input type="radio" id="noanswer" v-model="drone" value="noanswer">
          <label for="noanswer">Don't want to say</label>
        </p>
        <p>
          <label for="fullname">Full name</label><br>
          <input type="text" id="fullname" v-model="fn" required="required"
                 placeholder="First- and last name">
          <span>Message is: {{ fn }}</span>
        </p>
        <p>
          <label for="email">E-mail</label><br>
          <input type="email" id="email" v-model="em" required="required"
                 placeholder="E-mail address">
          <span>Message is: {{ em }}</span>
        </p>
        <!-- <p>
          <label for="street">Street name</label><br>
          <input type="text" id="street" v-model="str" placeholder="Street name">
          <span>Message is: {{ str }}</span>
        </p>
        <p>
          <label for="house">House number</label><br>
          <input type="number" id="house" v-model="hou" placeholder="House number">
          <span>Message is: {{ hou }}</span>
        </p>
        -->
        <p>
          <label for="payment">Recipient</label><br>
          <select id="payment" v-model="pay">
            <option selected="selected">Card</option>
            <option>Cash</option>
            <option>Bitcoin</option>
            <option>Invoice</option>
          </select>
        </p>
        <p>
          <label for="other">Other information</label><br>
          <textarea id="other" v-model="other"></textarea>
        </p>
        <button type="submit" v-on:click="printOrder">
          Send order!
        </button>
      </form>
    </section>
  </main>
  <hr>
  <footer>
    <p>Very nice maker made this (Felix)</p>
  </footer>
</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

function MenuItem(burg, kcal, url, glu, lac, veg) {
  this.name = burg; // The *this* keyword refers to the object itself
  this.kCal = kcal;
  this.urlPic = url;
  this.gluten = glu;
  this.lactose = lac;
  this.vegan = veg;
  return this;
}

const barg = new MenuItem("The Fire Burger", 300, "grillad_burgare.jpeg", true, false, false);
const birg = new MenuItem("The Nacho Burger", 400, "nacho_burger.jpeg", true, true, false);
const berg = new MenuItem("The Vegan Burger", 500, "vegan_burger.jpeg", false, false, true);

const burgershh = [barg, birg, berg];

console.log(burgershh);

export default {
  name: 'Home',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      drone: '',
      fn: '',
      em: '',
      pay: '',
      other: '',
      orderedBurgers: {},
      orderNumber: 0,
      location: {
        x: 0,
        y: 0
      },
    }
  },
  methods: {
    getOrderNumber: function () {
      this.orderNumber += 1
      return this.orderNumber;
    },
    addOrder: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      socket.emit("addOrder", {
            orderId: this.getOrderNumber(),
            details: {
              x: event.clientX - 10 - offset.x,
              y: event.clientY - 10 - offset.y
            },
            orderItems: ["Beans", "Curry"]
          }
      );
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
    printOrder: function () {
      let orde = [this.drone, this.fn, this.em,this.pay, this.other]
      console.log(orde)
      console.log(this.orderedBurgers)

      socket.emit("addOrder", {
            orderId: this.getOrderNumber(),
            details: {
              drone:this.drone,
              fn:this.fn,
              em:this.em,
              pay:this.pay,
              other:this.other,
              x:this.location.x,
              y:this.location.y
            },
            orderItems: [this.orderedBurgers]
          }
      );
    },
    setLocation: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      }
      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y
      }
    },
  }
}

</script>

<style>
  #map {
    width: 1920px;
    height: 1078px;
    background: url("../../public/img/polacks.jpg");
    background-repeat: no-repeat;
    position: relative;
    margin: 0;
    padding: 0;
    cursor: crosshair;
  }
  #map div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
  #cont {
    width:400px;
    height:400px;
    overflow:scroll;
  }
  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300&display=swap');

  template {
    font-family: 'Roboto', sans-serif;
  }

  .container {
    position: relative;
    text-align: center;
    color: white;
  }

  #header-background {
    width:100%;
    height: 15em;
    opacity:70%;
  }

  #header-text {
    position: absolute;
    top: 50%;
    left: 50%;
    z-index: 1;
    transform: translate(-50%, -50%);
  }

  section {
    margin: 0 20px 0 20px;
    padding: 20px;
    background-color:black;
    color:white;
    border: 4px solid #E18728;
    border-radius: 10px;
  }

  .wrapper {
    display: grid;
    grid-gap:10px;
    background-color: #434241;
    border-radius: 5px;
    padding: 10px;
    grid-template-columns: auto auto auto;
  }

  #burgers {
    margin-bottom: 20px;
  }

  button {
    margin: 30px 0 30px 0;
    height: 3em;
    width: 8em;
  }

  button:hover {
    background-color:blue;
  }
</style>
