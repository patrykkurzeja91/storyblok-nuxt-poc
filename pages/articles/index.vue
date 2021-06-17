<template>
  <section>
    <h2 class="py-10 text-center font-bold text-4xl">
      Articles Overview
    </h2>
    <ul class="container grid-wrapper py-6 mb-6 mx-auto">
      <li
        v-for="article in sortedStories"
        :key="article.uuid"
        class=""
      >
        <article-teaser
          v-if="article.content"
          :article-link="article.full_slug"
          :article-content="article.content"
        />
        <p
          v-else
          class="px-4 py-2 text-white bg-red-700 text-center rounded"
        >
          This content loads on save. <strong>Save the entry & reload.</strong>
        </p>
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  asyncData (context) {
    const version = context.query._storyblok || context.isDev ? 'draft' : 'published'
    return context.app.$storyapi.get('cdn/stories', {
      starts_with: 'articles/',
      version,
    }).then((res) => {
      return { stories: res.data.stories }
    }).catch((res) => {
      if (!res.response) {
        console.error(res)
        context.error({ statusCode: 404, message: 'Failed to receive content form api' })
      } else {
        console.error(res.response.data)
        context.error({ statusCode: res.response.status, message: res.response.data })
      }
    })
  },
  computed: {
    sortedStories () {
      return this.stories.map(i => i).sort((a, b) => new Date(b.created_at) - new Date(a.created_at))
    },
  },
}
</script>

<style scoped>
.grid-wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-gap: 20px;
  background-color: #fff;
  color: #444;
}
</style>
