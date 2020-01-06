<template>
  <div>
    <Header />
    <section class="single">
      <div class="head">
        <div
          v-if="post.fields.image == undefined"
          class="img"
          :style="'background-image: url(' + defaultImg + ');'
            +'background-repeat:' + repeat + ';'
            +'background-size:' + size + ';'
            +'background-position:' + position + ';'
            +'background-attachment:' + attachment + ';'
            +'filter:' + filter + ';'"
        ></div>
        <div
          v-else
          class="img"
          :style="'background-image: url(' + post.fields.image.fields.file.url + ');'
            +'background-repeat:' + repeat + ';'
            +'background-size:' + size + ';'
            +'background-position:' + position + ';'
            +'background-attachment:' + attachment + ';'
            +'filter:' + filter + ';'"
        ></div>
        <div class="text-box">
          <nuxt-link
            :to="{
              name: 'posts-getby-query',
              params: {
                getby: 'category',
                query: post.fields.category.fields.slug
              }
            }"
          >
            <p
              v-if="post.fields.category != undefined"
              class="category"
            >{{ post.fields.category.fields.name }}</p>
          </nuxt-link>
          <h1 v-if="post.fields.title != undefined">{{ post.fields.title }}</h1>
          <div v-if="post.fields.tags != undefined" class="tags">
            <p
              v-for="tag in post.fields.tags"
              :key="tag.sys.id"
              @click="$router.push({
                name: 'posts-getby-query',
                params: {
                  getby: 'tag',
                  query: tag.sys.id
                }
              })"
            ># {{ tag.fields.name }}</p>
          </div>
        </div>
      </div>
      <div class="contents">
        <div
          v-if="post.fields.intro != undefined"
          class="post-intro"
          :style="'font-size:'+fontSize+'; line-height:'+lineHeight+';'"
          v-html="post.fields.intro"
        ></div>
        <div
          v-if="post.fields.content != undefined"
          class="post-body"
          :style="'font-size:'+fontSize+'; line-height:'+lineHeight+';'"
          v-html="post.fields.content"
        ></div>
        <Rec
          v-if="post.fields.tags != undefined && filterBy(postsRec, post.fields.tags[0].fields.name, 'fields.content').length > 0"
          :posts="filterBy(postsRec, post.fields.tags[0].fields.name, 'fields.content')"
        />
        <Rec v-else :posts="postsFeatured" />
      </div>
    </section>
  </div>
</template>

<script>
import { createClient } from "~/plugins/contentful.js";
import siteConfig from "~/siteConfig.json";
import Header from "~/components/Header.vue";
import List from "~/components/List.vue";
import Rec from "~/components/Rec.vue";
import Vue2Filters from "vue2-filters";

const client = createClient();

export default {
  head() {
    return {
      title: `${this.post.fields.title} | ${siteConfig.title} - ${siteConfig.subtitle}`,
      meta: [
        {
          hid: `description`,
          name: "description",
          content: `${this.post.fields.title} | ${siteConfig.title} - ${siteConfig.subtitle}`
        }
      ],
      link: [
        {
          rel: "stylesheet",
          href:
            "https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.5.1/katex.min.css"
        },
        {
          rel: "stylesheet",
          href:
            "https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.15.6/styles/atom-one-dark.min.css"
        }
      ]
    };
  },
  asyncData({ env, params }) {
    return Promise.all([
      client.getEntries({
        "sys.id": env.CTF_AUTHOR_ID
      }),
      client.getEntries({
        content_type: "post",
        "fields.slug": params.slug
      }),
      client.getEntries({
        content_type: "post",
        order: "-sys.createdAt"
      }),
      client.getEntries({
        content_type: "post",
        "fields.slug[nin]": params.slug,
        order: "-sys.createdAt"
      }),
      client.getEntries({
        content_type: "post",
        "fields.slug[nin]": params.slug,
        "fields.featured": true,
        order: "-sys.createdAt"
      }),
      client.getEntries({
        content_type: "post",
        order: "-fields.date"
      }),
      client.getEntries({
        content_type: "tag",
        order: "-sys.createdAt"
      })
    ])
      .then(
        ([
          authors,
          post,
          posts,
          postsRec,
          postsFeatured,
          postsLatest,
          tags
        ]) => {
          return {
            author: authors.items[0],
            post: post.items[0],
            posts: posts.items,
            postsRec: postsRec.items,
            postsFeatured: postsFeatured.items,
            postsLatest: postsLatest.items,
            tags: tags.items
          };
        }
      )
      .catch(console.error);
  },
  data() {
    return {
      title: siteConfig.title,
      subtitle: siteConfig.subtitle,
      fontSize: siteConfig.postOption.fontSize,
      lineHeight: siteConfig.postOption.lineHeight,
      repeat: siteConfig.backgroundImageOption.repeat,
      size: siteConfig.backgroundImageOption.size,
      position: siteConfig.backgroundImageOption.position,
      attachment: siteConfig.backgroundImageOption.attachment,
      filter: siteConfig.backgroundImageOption.filter,
      defaultImg: siteConfig.postOption.defaultImg
    };
  },
  components: {
    Header,
    List,
    Rec
  },
  mixins: [Vue2Filters.mixin]
};
</script>

<style lang="stylus">
.single {
  .head {
    width: 100%;
    position: relative;
    overflow: hidden;
    text-align: center;
    min-height: 350px;

    .img {
      position: absolute;
      top: 0;
      bottom: 0;
      width: 100%;
      z-index: -1;
    }

    .text-box {
      padding: 10px 0 30px;

      .category {
        display: inline-block;
        padding: 5px 10px;
        border-bottom: 3px solid #555;
      }

      h1 {
        font-size: 2rem;
        margin: 10px auto 20px;
        width: 60%;
      }

      .date {
        font-size: 1rem;
      }

      .tags p {
        background: #eee;
        padding: 0 10px;
        height: 25px;
        line-height: 25px;
        border-radius: 5px;
        display: inline-block;
        font-size: 0.8rem;
        margin: 2px;
        cursor: pointer;
      }
    }
  }

  .contents {
    width: 600px;
    margin: 0 auto;

    .post-intro {
      margin: 30px auto;
      padding: 0 5px;

      h1, h2, h3, h4, h5, h6 {
        text-align: left;
        margin: 5px 0;
        padding: 0;
      }

      p {
        margin: 0;
      }
    }

    .intro:after {
      display: none;
    }

    .post-body {
      margin: 10px auto 100px;
      padding: 0 5px;

      .table-of-contents {
        position: fixed;
        width: 18%;
        top: 70px;
        right: 5px;
        border-radius: 5px;
        padding: 0 5px;
        z-index: 10;
        background: #eee;

        ul {
          margin: 0;
          padding: 0;

          li {
            list-style: none;
            font-size: 0.9rem;
            line-height: 1.2rem;
            margin: 10px 0;
            padding: 5px;
            border-radius: 5px;

            ul {
              margin: 0;

              li {
                list-style-type: disc;
                margin: 5px 0 5px 20px;
                padding: 0;
                font-size: 0.8rem;
                line-height: 1rem;
              }
            }
          }
        }
      }

      :link, :visited {
        border-bottom: 2px solid #eee;
        transition: 0.2s;
      }

      :link:hover, :visited:hover {
        border-bottom: 2px solid #555;
      }

      p {
        margin: 30px 0;
        text-align: left;
      }

      h1 {
        margin: 50px 0 30px;
        padding: 50px 0 10px;
        font-size: 1.5rem;
        text-align: center;
      }

      h2 {
        font-size: 1.2rem;
        margin: 30px 0 0;
        padding-left: 10px;
        border-left: 5px solid #555;
      }

      h3 {
        font-size: 1rem;
      }

      iframe {
        width: 100%;
        min-height: 50px;
        margin: 0 auto;
      }

      img {
        width: calc(100% - 10px);
        border: 5px solid #eee;
        margin: 10px auto;
        display: block;
        margin: 0;
      }

      .bubble {
        display: flex;
        flex-wrap: no-wrap;
        justify-content: flex-start;
        width: 70%;
        margin: 30px auto;
        position: relative;

        img {
          width: 60px;
          height: 60px;
          max-width: 56px;
          max-height: 60px;
          min-width: 60px;
          min-height: 60px;
          border-radius: 50%;
          border: 2px solid #eee;
          margin: 0 10px;
        }

        p {
          margin: 0;
          padding: 0;
        }

        p:nth-child(2) {
          position: absolute;
          top: 65px;
          left: 10px;
          width: 60px;
          text-align: center;
          font-size: 0.7rem;
          line-height: 1rem;
        }

        p:nth-child(3) {
          border: 1px solid #eee;
          padding: 6px 10px;
          border-radius: 5px;
          font-size: 0.8rem;
          line-height: 1.2rem;
          min-width: 70%;
        }
      }

      .bubble-i {
        display: flex;
        flex-wrap: no-wrap;
        justify-content: flex-end;
        width: 70%;
        margin: 30px auto;
        position: relative;

        img {
          width: 60px;
          height: 60px;
          max-width: 56px;
          max-height: 60px;
          min-width: 60px;
          min-height: 60px;
          border-radius: 50%;
          border: 2px solid #eee;
          margin: 0 10px;
        }

        p {
          margin: 0;
          padding: 0;
        }

        p:first-child {
          border: 1px solid #eee;
          padding: 6px 10px;
          border-radius: 5px;
          font-size: 0.8rem;
          line-height: 1.2rem;
          min-width: 70%;
        }

        p:nth-child(2) {
          position: absolute;
          top: 65px;
          right: 10px;
          width: 60px;
          text-align: center;
          font-size: 0.7rem;
          line-height: 1rem;
        }
      }

      table {
        margin: 0 auto;

        th {
          background: #555;
          color: white;
        }

        td {
          padding: 5px 10px;
          background: #eee;
          font-size: 0.9rem;
          width: 30%;
        }
      }

      code {
        margin: 3px;
        padding: 3px;
        border-radius: 2px;
        font-weight: bold;
        font-family: 'Courier New', Courier, monospace;
        background: #eee;
        line-height: 1rem;
      }

      .hljs {
        background: rgba(0, 0, 0, 0.9);
        padding: 25px;
        border-radius: 5px;
        font-size: 0.9rem;
      }

      .result {
        border: 1px solid #eee;
        padding: 10px 20px;
        margin: -15px 3px 0;

        h1 {
          margin: 0;
          padding: 10px 0 0;
        }
      }

      blockquote {
        border-left: 5px solid #eee;
        border-bottom: 5px solid #eee;
        margin-left: 10px;
        padding: 20px 0 20px 20px;
        font-size: 0.8rem;
        line-height: 1.6rem;

        p {
          margin: 0;
        }
      }
    }
  }
}

@media (max-width: 1000px) {
  .single {
    .head {
      .text-box {
        h1 {
          width: auto;
        }
      }
    }

    .contents {
      width: 80%;

      .post-body {
        margin: 50px auto;

        .table-of-contents {
          position: relative;
          width: auto;
          top: auto;
          right: auto;
          background: #eee;
          border-radius: 5px;
          margin: 20px auto;
          padding: 10px 20px;

          ul {
            li {
              background: none;
            }
          }
        }
      }
    }
  }
}

@media (max-width: 768px) {
  .single {
    .head {
      width: 100%;
      margin: 10px 0 0;
      min-height: 200px;

      .text-box {
        top: 3vw;
      }

      .text-box {
        h1 {
          font-size: 1.5rem;
          width: 80%;
        }
      }
    }

    .contents {
      width: 98%;

      .post-body {
        padding: 5px;
        margin: 0 auto;
        text-align: justify;

        h1 {
          margin: 50px auto 0;
          padding-top: 20px;
          text-align: center;
          font-size: 1.2rem;
        }

        h2 {
          margin: 0;
          border-radius: 0;
          font-size: 1rem;
          line-height: 2rem;
        }

        .hljs {
          background: rgba(0, 0, 0, 0.9);
          border-radius: 0;
          padding: 20px;
          margin: 0 -2.5%;
        }

        .bubble, .bubble-i {
          width: 85%;
        }
      }
    }
  }
}
</style>

