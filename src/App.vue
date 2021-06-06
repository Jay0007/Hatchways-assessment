<template>
  <v-app>
    <v-main>
      <mainpage />
    </v-main>
  </v-app>
</template>

<script>
import mainpage from "./components/mainpage";

export default {
  name: "App",

  components: {
    mainpage,
  },

  data: () => ({
    //
  }),
};
</script>
