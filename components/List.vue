<template>
  <section class="post-list">
    <div class="section-title">
      <h2>{{ category.name }}</h2>
      <p v-if="category.description != undefined" class="subtitle">{{ category.description }}</p>
    </div>

    <div class="posts">
      <BoxItem v-for="post in orderBy(limitBy(filterBy(posts, category.slug, 'fields.category.fields.slug'), shownumTop), listPostsOrder, listPostsOrderDirection)" :key="post.sys.id" :post="post" />
    </div>

    <p class="more-btn">
      <nuxt-link
        :to="{
          name: 'posts-getby-query',
          params: {
            getby: 'category',
            query: category.slug
          }
        }"
      >もっとみる</nuxt-link>
    </p>

  </section>
</template>

<script>
import Vue2Filters from "vue2-filters";
import BoxItem from "~/components/BoxItem.vue";
import siteConfig from "~/siteConfig.json";

export default {
  props: ["posts", "category"],
  mixins: [Vue2Filters.mixin],
  data() {
    return {
      shownumTop: siteConfig.listOption.shownumTop,
      listPostsOrder: siteConfig.listOption.listPostsOrder,
      listPostsOrderDirection: siteConfig.listOption.listPostsOrderDirection
    };
  },
  components: {
    BoxItem
  },
  computed: {
    // TODO: Postのフィルタを関数で行う（ `orderBy limitBy filterBy`の部分）
    // orderBy: () => {
    //   return this.posts.filter(
    //     item => item <= siteConfig.listOption.listPostsOrder
    //   );
    // },
    // limitPosts: () => {
    //   return this.posts.filter(
    //     item => item <= siteConfig.listOption.shownumTop
    //   );
    // },
    // filteredPosts: () => {
    //   return this.posts.filter(item => item <= category.slug);
    // }
  }
};
</script>

<style lang="stylus">
.section-title {
  text-align: center;
  padding: 1rem 0;
  border-radius: 5px;
  margin-bottom: 10px;

  h1, h2 {
    margin: 0;
    font-size: 1.4rem;
  }

  .subtitle {
    margin-top: 1rem;
    font-size: 0.9rem;
  }
}

.post-list {

  .posts {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .more-btn {
    width: 100%;
    max-width: 150px;
    margin: 10px auto;
    text-align: center;
    padding: 10px 0;
    border: 1px solid #eee;
    border-radius: 30px;
    font-weight: bold;
    transition: 0.2s;

    &:hover {
      border: 1px solid #555;
    }
  }
}

.post-list {
  margin: 5rem 0;
  &:first-of-type {
    margin: 3rem 0 5rem;
  }
  &:not(:last-of-type) {
    &:after {
      content: '';
      display: block;
      height: 5px;
      width: 100px;
      background: #eee;
      margin: 50px auto;
      border-radius: 10px;
    }
  }
}
</style>


