<template lang='pug'>
  .drops.p-6
    h1.text-6xl.mb-2 Lynn Benson
    h2.text-2xl.mb-2 Drops
    .mb-4
      input.shadow-md.border-2.rounded-md.p-2(v-model='q' placeholder='Search' class='hover:border-red-900')
    .mb-4
      h3 Showing {{drops.length}} of 844
    .grid.gap-5(class='grid-cols-2 md:grid-cols-5')
      .image(v-for='drop in drops')
        nuxt-img.mb-3.bg-red-50(:src="`images/${imageFile(drop.id)}`" sizes='sm:400px md:600px')
        p {{drop.content}}
</template>

<script>
export default {
  name: 'IndexPage',
  // https://docs.google.com/spreadsheets/d/e/2PACX-1vReJ5d0iwWnJRf8bW_JIF1sQD7_wHV_londa8S3fjHv_vRSqx1dVzlzxU11_RKaF_shdVAk28cbqDaa/pub?gid=1774686217&single=true&output=csv
  data() {
    return {
      q: null
    }
  },
  watch: {
    async q() {
      if(this.q.length > 3) {
        this.drops = await this.$content('drops')
          .search('content', this.q)
          .fetch()
      }
      else {
        this.drops = await this.$content('drops')
          .sortBy('id')
          .limit(40)
          .fetch()
      }
    }
  },
  async asyncData( { $content, params }) {
    const drops = await $content('drops')
      .sortBy('id')
      .limit(40)
      .fetch()
    return { drops }
  },
  methods: {
    imageFile(number) {
      const file = ('0000' + number).substr(-4, 4)
      try {
        require(`../static/images/${file}.jpg`)
        return `${file}.jpg`
      }
      catch (e) {
        return 'noimage.png'
      }
    },
    imageName(number) {
      return ('0000' + number).substr(-4, 4)
    }
  }
}
</script>
<style lang='sass'>
</style>
