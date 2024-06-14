<template>
  <div>
    <header-navbar />
    <list-items />
    <footer-component />
  </div>
</template>

<script>
import HeaderNavbar from '~/components/HeaderNavbar.vue'
import ListItems from '~/components/ListItems.vue'
export default {
  components: { ListItems, HeaderNavbar },
  name: 'IndexPage',
}
</script>
