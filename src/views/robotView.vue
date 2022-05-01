<template>
  <robotMenu>
  </robotMenu>
  
</template>

<script>
// @ is an alias to /src
import robotMenu from '@/components/robotMenu.vue'


export default {
  
  name: 'robotView',
  components: {
    robotMenu,
  },
  beforeCreate() {
    let image = require('../assets/tactile_noise.png')
    document.querySelector('body').setAttribute('style',"background-image: url(" + image + ");")
  },
  beforeUnmount () {
  document.querySelector('body').removeAttribute('style')
  }

}



</script>
