<template>
  <div class="container">
    <Quiz />
  </div>
</template>

<script>
import Quiz from "~/components/Quiz.vue";
export default {
  components: { Quiz },
};
</script>
<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
