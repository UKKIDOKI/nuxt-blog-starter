<template>
  <main class="blog-post-text">
    <ContentDoc>
      <template v-slot="{ doc }">
        <!-- Headline -->
        <h1
          class="blog-post-text font-bold mb-4 md:mb-6 text-h3 leading-h3 md:text-h1 md:leading-h1 text-center md:text-left"
        >
          {{ doc.headline }}
        </h1>
        <p
          class="blog-post-text mb-8 md:w-8/12 md:text-lg md:leading-lg text-center md:text-left"
        >
          {{ doc.excerpt }}
        </p>

        <!-- Social Share -->
        <!-- Content -->
        <Section
          id="main"
          class="!pt-0 relative grid grid-cols-10 gap-8 lg:gap-12"
        >
          <!-- Table of Contents -->
          <aside class="col-span-full md:col-span-3 md:hidden">
            <div class="blog-post-text blog-aside-wrapper mb-2">
              <BlogTableOfContents :links="doc.body?.toc?.links" />
            </div>
          </aside>
          <article class="prose col-span-full md:col-span-7 relative">
            <!-- Update date -->
            <span
              v-show="doc.dateUpdated"
              class="italic absolute -top-8 text-sm leading-sm font-light text-typography_primary/75 dark:text-typography_primary_dark/75"
              >(Updated: {{ $formatDate(doc.dateUpdated) }})</span
            >
            <!-- Blog content -->
            <ContentRenderer :value="doc" class="blog-content blog-post-text" />
          </article>
          <aside class="col-span-full md:col-span-3 blog-aside h-fit">
            <!-- Mobile Table of Content -->
            <div class="!hidden blog-aside-wrapper md:!flex mb-4">
              <BlogTableOfContents
                :links="doc.body?.toc?.links"
                class="blog-post-text"
              />
            </div>
            <!-- Related articles -->
          </aside>
        </Section>
        <!-- Scroll to top -->
      </template>
      <!-- Error in case not found -->
      <template #not-found>
        <SectionsError />
      </template>
    </ContentDoc>
  </main>
</template>

<script setup>
const { $formatDate } = useNuxtApp();
const { path } = useRoute();
const cleanPath = path.replace(/\/+$/, "");
const { data, error } = await useAsyncData(`content-${cleanPath}`, async () => {
  // Remove a trailing slash in case the browser adds it, it might break the routing
  // fetch document where the document path matches with the cuurent route
  let article = queryContent("/blog").where({ _path: cleanPath }).findOne();
  // get the surround information,
  // which is an array of documeents that come before and after the current document
  let surround = queryContent("/blog")
    .sort({ date: -1 })
    .only(["_path", "headline", "excerpt"])
    .findSurround(cleanPath, { before: 1, after: 1 });
  return {
    article: await article,
    surround: await surround,
  };
});

// Get the authors
const { data: authorData } = await useAsyncData("home", () =>
  queryContent("/authors").findOne()
);

// Set the meta
const baseUrl = "https://example.com";
const canonicalPath = baseUrl + (path + "/").replace(/\/+$/, "/");
const image =
  baseUrl + (data.value?.article?.socialImage.src || "/sample.webp");
</script>

<style scoped></style>
