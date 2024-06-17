<template>
  <div>
    <HeaderNavbar />
    <ListItems />
    <FooterComponent />
  </div>
</template>

<script>
import FooterComponent from '~/components/FooterComponent.vue';
import HeaderNavbar from '~/components/HeaderNavbar.vue';
import ListItems from '~/components/ListItems.vue';
export default {
  name: 'IndexPage',
  components: { ListItems, HeaderNavbar, FooterComponent },
}
</script>
