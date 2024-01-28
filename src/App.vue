<script>
import { RouterLink, RouterView } from 'vue-router'
// import HelloWorld from './components/HelloWorld.vue'
// export default {
//   components: {
//     HelloWorld
//   }
// }
export default {
  components: {
    RouterLink,
    RouterView
  }
}
</script>

<template>
  <div>
    <RouterLink to="/"></RouterLink>
    <RouterView />
  </div>
</template>
