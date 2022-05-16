<template lang='pug'>
  .drops.p-8
    Header
    .mb-4
      pre {{query}}
      input.shadow-md.border-2.rounded-md.p-2.w-80(v-model='q' placeholder='Search' class='focus-within:border-green-400 hover:border-green-800')
    .mb-4
      h3 Showing {{drops.length}} of 844
    .grid.gap-5(class='grid-cols-2 md:grid-cols-5')
      .image(v-for='drop in drops')
        .mb-3
          router-link(:to='`${drop.id}`' v-if='drop.photo')
            nuxt-img.bg-red-50(:src="`images/${imageFile(drop.id)}`" sizes='sm:400px md:600px')
          .noimage.bg-red-50(v-else)
            nuxt-img(src='images/noimage.png')
            //- p Image Coming Soon
        p {{drop.content}}
</template>

<script>
import Header from '~/components/Header'

export default {
  components: { Header },
  name: 'Drops',
  data() {
    return {
      q: '',
    }
  },
  mounted() {
    // console.log(this.$route.query);
    this.q = this.$route.query.q
  },
  watch: {
     async q() {
       await this.sleep(300)
       this.$router.push({ query: { q: this.q }})
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
    },
  },
  async asyncData( { $content, params }) {
    const drops = await $content('drops')
      .sortBy('id')
      .limit(40)
      .fetch()
    return { drops }
  },
  methods: {
    async sleep(milliseconds=3200) {
      console.log('setting', milliseconds);
      return new Promise(resolve => setTimeout(resolve, milliseconds))
    },
    imageFile(number) {
      const file = ('0000' + number).substr(-4, 4)
      try {
        require(`../static/images/${file}.jpg`)
        return `${file}.jpg`
      }
      catch (e) {
        return 'camera.png'
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
