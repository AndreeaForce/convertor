<template>
    <div id="app">
        <!--<app-header></app-header>-->
        <!--<app-meniu @sendIngredient="ingredients=$event"></app-meniu>-->
        <app-ingredients></app-ingredients>
        
    </div>
</template>

<script>
import Header from './components/Header.vue'
import Ingredients from './components/Ingredients.vue'
import Meniu from './components/Meniu.vue'

export default {
    components: {
        'app-header' : Header,
        'app-ingredients': Ingredients,
        'app-meniu': Meniu
    },

  name: 'app',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
    }
  },
    methods: {
        
    }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {

  margin: 10px;
}

a {
  color: #42b983;
}
</style>
