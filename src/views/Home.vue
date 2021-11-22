<template>

  <header>
    <img id="headimg" src="https://www.freewpheaders.com/wp-content/gallery/food-gallery/burger-sandwich-header.jpg" alt="headimg">
    <img id="flyingburger" src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQGzVDllssE7SBo-U5cplirSv6pfdSedUKTmw&usqp=CAU" alt="bewinged burger">
    <h1 id="headtxt"> Welcome to BurgerParadise&reg; Online</h1>
    <h3 id="headtxt2"> - Eat a burger wherever, whenever</h3>

  </header>

  <main>

    <section id="burgersection">
        <div class="burgerhead">
          <h3> Please select a burger from our menu below </h3>
          <p> We got three different delicious alternatives for you to choose from</p>
        </div>

        <div class="burgerwrapper">
          <Burger v-for="burger in burgers"
                  v-bind:burger="burger"
                  v-bind:key="burger.name"
                  v-on:orderedBurger="addToOrder($event)"/>
        </div>

      <br />
    </section>

    <section id="submitsection">
      <div class="wrapmap">

        <div  id="customertext">
        <h2>Customer Information</h2>
        <h4 style="margin-bottom:2em">All fields below must be filled in to complete your order</h4>
        </div>

        <form id="formcustomer">

        <p>
          <span>Fullname</span><br>
          <input v-model="firstlastname" placeholder="First- and Last name">
        </p>
        <p>
          <span>E-mail</span><br>
          <input v-model="email" placeholder="E-mail adress">
        </p>
<!--        <p>
          <span>Street</span><br>
          <input v-model="street" placeholder="Street name">
        </p>
        <p>
          <span>House</span><br>
          <input type="number" v-model="house" placeholder="House number">
        </p>-->
        <p>
          <span>Other information (max 50 words)</span><br>
          <textarea v-model="textarea" placeholder="Write here" rows="4" cols="50" maxlength="50"></textarea>
        </p>

        <p>
          <label>How to pay: </label>
          <select v-model="pay" >
            <option>MasterCard</option>
            <option>Klarna</option>
            <option>Swish</option>
            <option>Cash when delivered</option>
          </select>
        </p>

        <div>
          <input type="radio" id="male" v-model="sex" value="Male">
          <label for="male">Male</label>
        </div>

        <div>
          <input type="radio"  id="female" v-model="sex" value="Female">
          <label for="female">Female</label>
        </div>

        <div>
          <input type="radio"  id="nonbinary" v-model="sex" value="Nonbinary">
          <label for="nonbinary">Non-binary</label>
        </div>

        <div>
          <input type="radio" checked="checked"  id="nan" v-model="sex" value="Undisclosed">
          <label for="nan">Undisclosed</label>
        </div>

      </form>
        <div id="choosetext">
        <h2 > Choose your delivery location:</h2>
        <h4>The chosen location will be our drop site</h4>
        </div>

          <div id="mapper">
              <div id="map" v-on:click="setLocation" >
                  <div id="dotted" v-bind:style="{left: this.location.x + 'px',
                                                   top:  this.location.y + 'px'}">
                   T
                  </div>
              </div>
          </div>

      </div>

        <button id="button" type="submit" v-on:click="submitClick">
          <img src="https://www.iconninja.com/files/148/341/227/telegram-send-chat-media-message-icon.svg" style="height:1.1em;width:1.1em">
          Place order
        </button>

    </section>

  </main>

  <hr>
  <footer>
    &copy; 2021 BPO - A delicious burger provider near you
  </footer>

</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

//function MenuItem(name, url, kcal,lactose, gluten, meat, soy, price) {
  //this.name = name;
  //this.url = url;
  //this.kcal = kcal;
  //this.lactose = lactose;
  //this.gluten = gluten;
  //this.meat = meat;
  //this.soy = soy;
  //this.price = price;
//}

//const alright = new MenuItem("The AAAlright Burger", 'data:image/jpeg;base64,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', "470", true, false, true, false, "99:-")
//const juicy = new MenuItem("The Juicy Burger", 'https://www.realgreekrecipes.com/wp-content/uploads/2017/09/Greek-Burger.jpg',"678",false, true, false, true, "149:-")
//const VIP = new MenuItem("The SuperVIP Burger", 'https://i.insider.com/593849304cb1e4381501cbe8?width=1000&format=jpeg&auto=webp', "1500", true,true, true, false, "999:-")

//const burgermenu = [ alright, juicy, VIP ];



export default {
  name: "Home",
  components: {
    Burger
  },

  data: function () {
    return {
            burgers:menu,
      firstlastname:'',
              email: '',
           textarea: '',
                pay: 'MasterCard',
                sex: 'Undisclosed',
      location: { x: 0,
                  y: 0},
      orderedBurger: {}
    }
  },

  methods: {
    done: function () {

    },

    addToOrder: function (event) {
     return this.orderedBurger[event.name] = event.amount;
    },

    getOrderNumber: function () {
      return Math.floor(Math.random() * 100000);
    },

    setLocation: function (event) {
      let offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      this.location.x = event.clientX - 10 - offset.x
      this.location.y = event.clientY - 10 - offset.y
    },

    submitClick: function () {
      socket.emit("addOrder", {
               orderId: this.getOrderNumber(),
            details:{x: this.location.x,
                     y: this.location.y,
                  name: this.firstlastname,
                 email: this.email,
              textarea: this.textarea,
                gender: this.sex},
            orderItems: this.orderedBurger
          },

      );
    },
  }
  }

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Comfortaa&display=swap');

  #map {
    background: url("../img/polacks.jpg");
    width: 1920px;
    height: 1078px;

  }
  #mapper {
    overflow: scroll;
    position: relative;
    grid-column:2;
    width: 600px;
    height: 400px;
    cursor: crosshair;
  }
  #dotted {
    background-repeat: no-repeat;
    position: absolute;
    background: black;
    color: red;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }

  body {
    font-family: 'Comfortaa', cursive;
  }

  #header{
    height: 325em;
    width: 100%;
  }

  #headtxt{
    position: absolute;
    margin-top:-20%;
    color: lightblue;
    margin-left: 0.5em;
    font-weight: bold;
    font-size:2.6em;
  }
  #headtxt2 {
    position: absolute;
    color: lightblue;
    margin-left: 1.5em;
    margin-top: -14%;
    font-size:1.5em;
  }
  #headimg{
    width: 100%;
    height: auto;
    vertical-align: middle;
    border-radius: 2em 2em 2em 2em;
  }
  #flyingburger{
    position: absolute;
    border-radius: 100%;
    width: 3em;
    height: 3em;
    top: 11.5em;
    left: 30em;
  }

  #burgersection{
    background-color: #FAEBD7;
    border-radius: 2em 2em 2em 2em;
    overflow: hidden;
  }

  #submitsection{
    clear:both;
    background-color:lightblue;
    color:white;
    border:0.5em Dotted #FAEBD7;
    border-radius: 2em 2em 2em 2em;
  }

  .burgerwrapper{
    display: grid;
    grid-template-columns: 33% 33% 33%;
    grid-template-rows: 100%;
    place-content: center;

  }
  .wrapmap{
    display: grid;

  }

  #customertext {
    grid-column: 1;
    padding-left:1.5em;
  }

  #choosetext{
    grid-column: 2;
  }

  #formcustomer{
    padding-left:1.5em;
    grid-column: 1;
    grid-row: 2;
  }

  #button:hover {
    background-color:green;
    cursor: grab;
  }

  #button{
    margin-top:1.5em;
    margin-left:1.5em;
    margin-bottom:1.5em;
  }

  .burgerhead {
    grid-column: 1 / span 3;
    grid-row: 1;
    margin-left: 3em;
  }

</style>
