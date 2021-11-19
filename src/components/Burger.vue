
<template>

  <div class="allburgers">
    <h4 class="center">{{burger.name}}</h4>
    <img class="burgerimg" v-bind:src="burger.url">
    <h5 class="center"> Burger Information</h5>
    <ul class="burgercontent">
      <li>{{burger.kcal + " kcal"}} </li>
      <span v-if="burger.meat == true"><li>Contains <span class="allergi" >meat</span></li></span>
      <span v-if="burger.lactose == true"><li>Contains <span class="allergi" >lactose</span></li></span>
      <span v-if="burger.soy == true"><li>Contains <span class="allergi" >soy</span></li></span>
      <span v-if="burger.gluten == true"><li>Contains <span class="allergi" >gluten</span></li></span>
    </ul>

    <p class="font">{{burger.price}}</p>
    <span> <input id="add" type="button" v-on:click="increase($event)" value="Add Burger">
      <span> &nbsp; <img id="shoppingcart" src="https://previews.123rf.com/images/fokaspokas/fokaspokas1805/fokaspokas180500214/101169048-shopping-cart-icon-simple-linear-icon-with-thin-outline-on-transparent-background-.jpg">  </span>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{{amountOrdered}}&nbsp;
    <input id="del" type="button" v-on:click="decrease($event)" value="Delete Burger"></span>


  </div>

</template>

<script>
export default {
  name: 'Burger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    increase: function () {
      this.amountOrdered += 1;
      console.log(this.burger.name, this.amountOrdered)
      this.$emit('increase', {
            name: this.burger.name,
            amount: this.amountOrdered
          }
      );
    },
    decrease: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
      }
      console.log(this.burger.name, this.amountOrdered)
      this.$emit('decrease', {
            name: this.burger.name,
            amount: this.amountOrdered
          }
      )
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.allburgers {
}

.center{
 margin-left: 30%;
}

.burgercontent{
 margin-left: 25%;
}

.burgerimg{
  display: block;
  border-radius: 2em 2em 2em 2em;
  width:17em;
  max-width: 100%;
  height:17em;
  max-height: 100%;
  margin-left: 4em;

}

.allergi {
  text-transform: uppercase;
  font-weight: bold;
  color: #FF0000;
}
.font{
  font-size:1em;
  font-weight: bold;
  text-align: center;
}
#shoppingcart{
  position: absolute;
  top: 163%;
  border-radius: 20%;
  width: 2em;
  height: 2em;
}
#add{
  margin-top: 2%;
  margin-left:18%;
}
#del{
}
#add:hover{
  background-color:green;
  cursor: grab;
}
#del:hover{
  background-color:red;
  cursor: grab;
}

</style>
