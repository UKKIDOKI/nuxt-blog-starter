<template>
  <main>
    <ContentQuery
      path="/blog"
      :only="['headline', 'excerpt', 'date', 'tags', '_path', 'image']"
      :sort="{
        date: -1,
      }"
      :limit="blogCountLimit"
      v-slot="{ data }"
    >
      <BlogList :data="data" />
    </ContentQuery>
  </main>
</template>

<script setup>
// Find the number of blogs present
const blogCountLimit = 6;
</script>
