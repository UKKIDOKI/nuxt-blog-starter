<template>
  <main>
    <Section id="main" class="!pt-0">
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
    </Section>
  </main>
</template>

<script setup>
// Find the number of blogs present
const blogCountLimit = 6;
</script>
