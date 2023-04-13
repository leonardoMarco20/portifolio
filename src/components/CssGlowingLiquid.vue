<template>
  <q-page class="column flex q-col-gutter-sm q-pa-md svg-study-page">
      <div class="flex full-width justify-center">
        <q-btn color="primary" label="Codepen" target="_blank" @click="gotToCodePen" />
      </div>
      <div class="column flex justify-center q-gutter-xl">
        <div class="flex justify-center">
          <app-card class="flex items-center justify-center">
            <app-glow-liquid />
          </app-card>
        </div>
      </div>
    </q-page>
</template>
<script>

import AppCard from 'src/components/AppCard.vue'
import AppGlowLiquid from 'src/components/AppGlowLiquid.vue'
export default ({
  name: 'SVGStudy',

  data(){
    return {
    }
  },

  components:{
    AppCard,
    AppGlowLiquid
  },

  methods: {
    gotToCodePen () {
      window.open("https://codepen.io/leonardo-marco/pens/public");
    }
  },
})

</script>

<style lang="scss">

</style>