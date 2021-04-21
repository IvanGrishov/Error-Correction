<template>
  <div class="home">
    <ErrorCorrection/>
  </div>
</template>

<script>
// @ is an alias to /src
import ErrorCorrection from '@/components/ErrorCorrection.vue'

export default {
  name: 'Home',
  components: {
    ErrorCorrection
  }
}
</script>
