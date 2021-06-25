<template>
  <div v-html="compiledMarkdown" />
</template>

<script>
import marked from "marked";
export default {
  name: "MarkdownDisplay",
  props: {
    markdown: {
      type: String,
      required: true,
    },
  },
  mounted() {},
  computed: {
    compiledMarkdown() {
      return marked(this.markdown, {});
    },
  },
};
</script>

<style>
.row {
  padding: 12px;
}
</style>