<template>
  <div class="burger-item">
    <h3>{{ burger.name }}</h3>
    <img :src="burger.url" :alt="burger.name" :title="burger.name" style="width: 200px">
    <div>
      <button type="button" v-on:click="removeBurger"> - </button>
       {{ amountOrdered }}
      <button type="button" v-on:click="addBurger"> + </button>
    </div>
    <ul>
      <li>{{ burger.kCal }} kCal</li>
      <li v-if="burger.lactose">Contains <span class="allergen">lactose</span></li>
      <li v-if="burger.gluten">Contains <span class="allergen">gluten</span></li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    };
  },
  methods: {
    addBurger: function () {
      this.amountOrdered += 1;
      this.$emit('orderedBurger', { 
        name: this.burger.name, 
        amount: this.amountOrdered 
      });
    },
    removeBurger: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
        this.$emit('orderedBurger', { 
          name: this.burger.name, 
          amount: this.amountOrdered 
        });
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.allergen {
    font-weight: bold;
}

.burger-item {
    display: block; 
}
</style>