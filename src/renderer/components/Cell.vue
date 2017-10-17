<style scoped>
.Cell {
  text-align: center;
}
</style>

<template>
  <div class="Cell">
    <!-- {{ cellid }} -->
  </div>
</template>

<script>
export default {
  name: 'Cell',
  components: {
  },
  computed: {
  },
  mounted () {
    console.log('Cell is Mounted!')
  },
  props: {
    cellid: Number
  },
  data () {
    return {
    }
  },
  methods: {
  }
}
</script>

