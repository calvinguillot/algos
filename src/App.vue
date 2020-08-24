<template>
  <v-app> 
    <v-main>
      <Algos />
    </v-main>
  </v-app>
</template>

<script>
import Algos from "./components/Algos";

export default {
  name: "App",
  components: {
    Algos,
  },

  data: () => ({
    //
  }),
};
</script>
