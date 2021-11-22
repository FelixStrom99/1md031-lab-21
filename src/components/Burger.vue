<template>
  <div>
    <figure>
      <h2>{{ burger.name }}</h2>
      <img class="bild-burger" v-bind:src="burger.urlPic" alt="Span" title="{{ burger.name }}">
      <ul>
        <li v-if="burger.vegan">
          Quorn
        </li>
        <li v-else>
          Kött
        </li>
        <li v-if="burger.lactose">
          Laktos
        </li>
        <li v-if="burger.gluten">
          Gluten
        </li>
        <li>
          {{ burger.kCal }} kcal
        </li>
      </ul>
      <button type="button" v-on:click="addBurger">Add burger</button>
      <button type="button" v-on:click="decreaseBurger">Remove burger</button>
      <p>{{ amountOrdered }}</p>
    </figure>
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
    addBurger: function () {
      this.amountOrdered += 1;
      this.$emit('orderedBurger', { name:   this.burger.name,
            amount: this.amountOrdered
          }
      );
    },
    decreaseBurger: function () {
      this.amountOrdered -= 1;
      this.$emit('orderedBurger', { name:   this.burger.name,
            amount: this.amountOrdered
          }
      );
    },

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .bild-burger {
      width: 200px;
      height: 200px;
}


</style>
