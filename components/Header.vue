<template>
  <header class="l-header">
    <h1>
      <nuxt-link to="/">{{ pagetitle }}</nuxt-link>
    </h1>
  </header>
</template>

<script>
import siteConfig from "~/siteConfig.json";
export default {
  data() {
    return {
      pagetitle: siteConfig.title
    };
  }
};
</script>

<style lang="stylus">
.l-header {
  padding: 2rem;
  text-align: center;
  h1 {
    letter-spacing: 0.1rem;
  }
}
</style>