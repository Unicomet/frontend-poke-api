<template>
  <div>
    <div class="d-flex flex-column min-vh-100">
      <HeaderNavbar />
      <ListItems />
      <FooterComponent />
    </div>
  </div>
</template>

<script>
import FooterComponent from "~/components/FooterComponent.vue";
import HeaderNavbar from "~/components/HeaderNavbar.vue";
import ListItems from "~/components/ListItems.vue";
export default {
  name: "IndexPage",
  components: { ListItems, HeaderNavbar, FooterComponent },
};
</script>
