<template>
  <v-app>
    <v-row justify="center" align="center">
      <v-col md="6">
        <video-player />
      </v-col>
    </v-row>
  </v-app>
</template>

<script>
import VideoPlayer from "./components/VideoPlayer";

export default {
  name: "App",

  components: {
    VideoPlayer,
  },

  data: () => ({
    //
  }),
};
</script>
