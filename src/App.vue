<template>
  <h1>
    {{ message }}
  </h1>
  <HelloWorld />
</template>

<script>
import HelloWorld from "~/components/HelloWorld.vue";

export default {
  components: {
    HelloWorld,
  },
  data() {
    return {
      message: "Hello World!",
    };
  },
};
</script>
