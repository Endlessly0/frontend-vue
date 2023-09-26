<template>
  <div>
    <v-btn color="blue" class="mt-4">
      Block Button
    </v-btn>
    <hello-world />
  </div>
  
</template>

<script>
import HelloWorld from '../components/HelloWorld'

export default {
  name: 'Home',

  components: {
    HelloWorld
  }
}
</script>
