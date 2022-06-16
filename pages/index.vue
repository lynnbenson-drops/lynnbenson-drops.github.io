<template lang='pug'>
  .drops.p-8
    Header
    .mb-4
      b-form-input.shadow-md.border-2.rounded-md.p-2.mr-4.w-80(class='focus-within:border-green-400 hover:border-green-800' v-model='q' debounce='300' placeholder='Search')
      a.border-b-4.border-white.pb-2(href='/' class='hover:border-green-400' v-if='q.length > 0') Clear search
    .mb-4.flex.justify-between
      h3(v-if='total > 0') Showing {{first_drop}} - {{last_drop}} of {{total}}
      h3(v-else) No works found
      .flex
        b-pagination(v-model='page' :total-rows='total' :per-page='20' v-if='total > 0')
    .grid.gap-5.mb-10(class='grid-cols-2 md:grid-cols-5')
      .image(v-for='drop in drops')
        .mb-4
          router-link(:to='dropLink(drop)' v-if='drop.photo == "true"')
            nuxt-img(:src="`images/${imageFile(drop.id)}`" sizes='sm:200px md:400px lg:600px')
          .noimage(v-else)
            nuxt-img(src='images/noimage.png')
        div(v-html='drop._highlightResult.content.value')
    .flex.justify-end
      b-pagination(v-model='page' :total-rows='total' :per-page='20' v-if='total > 0')
</template>
<script>
import Header from '~/components/Header'

export default {
  components: { Header },
  name: 'Drops',
  data() {
    return {
      q: '',
      total: 0,
      page: null
    }
  },
  mounted() {
    console.log(this.$route);
    if(this.$route.query.q)
      this.q = this.$route.query.q
    if(this.$route.query.page)
      this.page = this.$route.query.page
  },
  async asyncData( { $algolia, query }) {
    const index = $algolia.initIndex('Drops')
    const q = query.q || ''
    const page = query.page - 1 || 0
    const results = await index.search(q, { page: page, hitsPerPage: 20 })
    return { drops: results.hits, q: q, total: results.nbHits, page: page + 1 }
  },
  watch: {
    the_query() {
      this.search()
    },
  },
  computed: {
    the_query() {
      var query = '?'
      if(this.q.length > 0)
        query += `q=${this.q}&`
      if(this.page > 1)
        query += `page=${this.page}&`
      
      return query
    },
    pages() {
      return Math.ceil(this.total / 20)
    },
    first_drop() {
      return 1 + (this.page-1) * 20
    },
    last_drop() {
      return this.first_drop + this.drops.length - 1
    }
  },
  methods: {
    async search() {
      const index = this.$algolia.initIndex('Drops')
      const results = await index.search(this.q, { page: this.page-1, hitsPerPage: 20 })
      this.$router.push({ query: { q: this.q, page: this.page }})
      this.drops = results.hits
      this.total = results.nbHits
    },
    imageFile(number) {
      const file = ('0000' + number).substr(-4, 4)
      return `${file}.jpg`
    },
    dropLink(drop) {
      var query = '?'
      if(this.q?.length > 0)
        query += `q=${this.q}&`
      if(this.page > 0)
        query += `page=${this.page}`
      return `${drop.id}${query}`
    },
    imageFilex(number) {
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
  em
    font-style: normal
    color: rgb(6, 95, 70)
    font-weight: 600
    // background: rgb(186, 186, 186)
  ul.pagination
    display: flex
    border: 1px solid #ccc
    li
      padding: 0px
      border-right: 1px solid #ccc
      display: flex
      align-items: center
      button
        padding: 4px 10px
        &:hover
          background: rgb(6, 95, 70)
          color: white
          pointer: cursor
      span
        padding: 0px 10px
      &:last-of-type
        border: 0px
      &.active
        background: #eee
</style>
