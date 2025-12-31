<template>
<div>
    <header>
        <h1>Welcome to BurgerHeaven Online</h1>
        <img src="../../public/img/background.jpg" alt="Restaurant">
    </header>
   <main>
    <section id="select-burger">
        <h2>Select burger</h2>
        <p>This is where you execute burger selection</p>
        
        <div class="wrapper">
          <Burger v-for="burger in burgers"
                  v-bind:burger="burger" 
                  v-bind:key="burger.name"
                  v-on:orderedBurger="addToOrder($event)"/>
        </div>
    </section>

    <section id="customer-info">
        <h2>Customer information</h2>
        <p>This is where you provide necessary information</p>
        <h3>Delivery Information</h3>
        <p>
            <label for="fullname">Full name</label><br>
            <input type="text" v-model="fullName" id="fullname" name="fn" required="required" placeholder="First- and Last name">
        </p>
        <p>
            <label for="E-mail">E-mail</label><br>
            <input type="email" v-model="email" id="email" name="em" required="required" placeholder="E-mail address">
        </p>
        <p>
        <label for="Payment options">Payment options</label><br>
        <select id="Payment options" v-model="paymentMethod" name="po" selected="Credit card">
            <option>Credit card</option>
            <option>Klarna</option>
            <option>Swish</option>
            <option>PayPal</option>
        </select>
        </p>
       <h3>Please indicate point of delivery:</h3> <div id="map-container">
          <div id="map" v-on:click="setLocation">
              <div class="dot" v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">
                  T
              </div>
          </div>
        </div>
        <form>
        <p>Gender</p>
            <input type="radio" id="male" name="gender" value="Male" v-model="gender" checked>
            <label for="male">Male</label><br>
            <input type="radio" id="female" name="gender" value="Female" v-model="gender">
            <label for="female">Female</label><br>
            <input type="radio" id="non-binary" name="gender" value="Non-binary" v-model="gender">
            <label for="non-binary">Non-binary</label><br>
            <input type="radio" id="prefer-not-to-say" name="gender" value="Prefer not to say" v-model="gender">
            <label for="prefer-not-to-say">Prefer not to say</label><br>
        </form><br>
    </section>
    <section id="delivery-map">

    </section>
    <button type="submit" v-on:click="addOrder">
        🛒 Place my order!
    </button>
   </main>
   <hr>
   <footer>
    &copy; 2018 Hypothetical Burgers Inc.
   </footer>
</div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'
const socket = io("localhost:3000");

function MenuItem(name, url, kCal, gluten, lactose) {
    this.name = name;
    this.url = url;
    this.kCal = kCal;
    this.gluten = gluten;
    this.lactose = lactose;
}

// const burgerArray = [
//     new MenuItem('Barger', '../../public/img/burger.webp', 300, true, true),
//     new MenuItem('Birger', '../../public/img/burger2.webp', 400, false, true),
//     new MenuItem('Berger', '../../public/img/burger3.webp', 500, true, false)
// ];

// console.log(burgerArray);

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      orderedBurgers: {},
      fullName: '',       
      email: '',  
      paymentMethod: 'Credit card', 
      gender: 'Male',
      location: { x: 0, y: 0 }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function (event) {
        var offset = {
            x: event.currentTarget.getBoundingClientRect().left,
            y: event.currentTarget.getBoundingClientRect().top
        };
        this.location.x = event.clientX - 10 - offset.x;
        this.location.y = event.clientY - 10 - offset.y;
    },
    addOrder: function () {
        socket.emit("addOrder", { 
            orderId: this.getOrderNumber(),
            details: { x: this.location.x, y: this.location.y },
            orderItems: this.orderedBurgers,
            fullName: this.fullName,
            email: this.email,
            paymentMethod: this.paymentMethod,
            gender: this.gender
        });
        console.log("Final Order submitted for:", this.fullName, this.location);
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
  }
}

</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
  #map-container {
      overflow: scroll;
      width: 100%;
      height: 400px;
      position: relative;
  }

  #map {
      width: 1920px;
      height: 1078px;
      background: url("/img/polacks.jpg"); 
      cursor: crosshair;
  }

  .dot {
      position: absolute;
      background: black;
      color: white;
      border-radius: 50%;
      width: 20px;
      height: 20px;
      text-align: center;
      line-height: 20px;
  }

  body {
      font-size: 12pt;
      font-family: arial;
  }

  p {
      color: red;
  }

  h1 {
      font-family: 'Agbalumo';
      font-size: 36pt;
  }
  main, header, footer, nav ul {
      max-width: 40rem;
      margin: 0 auto 0 auto;
  }
  main {
      background-color: bisque;
  }

  /* nav ul li {
      display: inline-block;
      background-color: grey;
      padding: 1em;
      margin: 1em;
  } */

  /* header {
      background-image: url("../img/background.jpg");
      background-size: cover;
      overflow: hidden;
      width: 100%;
      height: 200px;
      opacity: 0.5;
  } */

  section {
      margin: 2rem 0;
  }

  header {
      position: relative;
      max-width: 40rem;
      margin: 0 auto;
      height: 200px;
      overflow: hidden;
  }

  header img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      opacity: 0.5;
  }

  header h1 {
      position: absolute;  
      top: 50%;          
      left: 50%;         
      transform: translate(-50%, -50%);  
      margin: 0;
      z-index: 2;     
      color: black;   
  }

  nav ul {
      display: grid;
      grid-template-columns: repeat(auto-fill, 9.25em);
      gap: 1em;
      padding: 0;
  }

  nav li {
      display: block;
      background-color: grey;
      padding: 1em;
  }

  .Very-good {
      color: green;
  }

  .Master {
      color: green;
      font-weight: bold;
  }

  .allergen {
      font-weight: bold;
  }

  #select-burger {
      border: 2px solid blueviolet;
      background-color: black;
      color: white;
  }

  button {
      margin-top: 1.5rem;
  }

  button:hover {
      cursor: pointer;
  }

  #customer-info {
      border: 2px dashed black;
      padding: 20px; 
  }

  .wrapper {
      display: grid;
      grid-template-columns: repeat(3, 1fr); 
  }

  .burger-item {
      display: block; 
  }
</style>