<template>
  <v-card :elevation="2" height="100" width="100" light>
    <v-row v-if="acik" class="fill-height" align="center" justify="center">
      <h1 v-text="deger"></h1>
    </v-row>
  </v-card>
</template>
<script>
export default {
  props: {
    deger: {
      type: String,
      default: ''
    },
    acik: {
      type: Boolean,
      default: false
    }
  }
}
</script>
