<template>
  <div class="box-item">
    <div
      v-if="post.fields.image == undefined"
      class="img"
      :style="'background-image: url('+ defaultImg +');'"
    ></div>
    <div
      v-else
      class="img"
      :style="'background-image: url(' + post.fields.image.fields.file.url +');'"
    ></div>

    <p class="category">
      <nuxt-link
        :to="{ name: 'posts-getby-query',
        params: {
        getby: 'category',
        query: post.fields.category.fields.slug } }"
      >{{ post.fields.category.fields.name }}</nuxt-link>
    </p>

    <div class="text-box">
      <p>
        <nuxt-link :to="{
          name: 'posts-slug',
          params: {
            slug: post.fields.slug
          }
        }">{{ post.fields.title }}</nuxt-link>
      </p>

      <p class="date">{{ (new Date(post.fields.date)).toLocaleDateString() }}</p>

      <div v-if="post.fields.intro != undefined" class="intro" v-html="post.fields.intro"></div>
    </div>
  </div>
</template>

<script>
import siteConfig from "~/siteConfig.json";
export default {
  props: ["post"],
  data() {
    return {
      defaultImg: siteConfig.postOption.defaultImg
    };
  }
};
</script>

<style lang="stylus" scoped>
.box-item {
  width: 100%;
  margin: 0.5rem 2rem;
  padding: 10px 0;
  border-radius: 5px;
  position: relative;
  border: 1px solid #eee;
  transition: 0.2s;
  @media (min-width: 768px) {
    max-width: 250px;
    margin: 3rem 1rem;
  }
  &:hover {
    border: 1px solid #555;
  }


  .img {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    position: absolute;
    top: 10px;
    right: 10px;
    overflow: hidden;
    border: 1px solid #eee;
    background-repeat: no-repeat;
    background-size: cover;
  }

  .category {
    height: 30px;
    line-height: 30px;
    display: inline-block;
    font-size: 0.8rem;
    border-bottom: 2px solid #555;
    margin: 0;
    position: absolute;
    top: 10px;
    left: 10px;
    cursor: pointer;
  }

  .text-box {
    margin: 65px 10px 5px 10px;

    h1 {
      font-size: 1rem;
      margin: 0;
      padding: 5px 0;
      cursor: pointer;
    }

    .date {
      font-size: 0.8rem;
      margin: 0 10px 10px 0;
      text-align: right;
    }

    .tags p {
      padding: 0 10px;
      height: 25px;
      line-height: 25px;
      border: 1px solid #eee;
      border-radius: 5px;
      display: inline-block;
      font-size: 0.8rem;
      margin: 2px;
      cursor: pointer;
    }

    .intro {
      font-size: 0.7rem;
      margin: 10px 0;
    }
  }
}



</style>

