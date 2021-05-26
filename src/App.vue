<template>
  <v-container>
    <v-row justify="center">
      <v-col md="6">
        <video-player :videoURL="videoURL" />
      </v-col>
      <!-- <v-col md="6">
        <h1>Próximos vídeos</h1>
        <div class="playlist">
          <video-thumb
            v-for="video in playlist"
            :key="video.name"
            :thumb-details="video"
            @click="changeVideoPlaying()"
          />
        </div>
      </v-col> -->
    </v-row>
  </v-container>
</template>

<script>
import VideoPlayer from "./components/VideoPlayer";
// import VideoThumb from "./components/VideoThumb";

const playlist = [
  {
    name: "Brinquedo",
    url: "http://techslides.com/demos/sample-videos/small.mp4",
  },
  {
    name: "Desenho",
    url: "https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4",
  },
];

export default {
  name: "App",

  components: {
    VideoPlayer,
    // VideoThumb,
  },

  data: () => ({
    videoURL:
      "https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4",
    playlist,
  }),
  methods: {
    changeVideoPlaying(url) {
      this.videoURL = url;
    },
  },
};
</script>

<style>
body {
  font-family: Roboto;
}
</style>
