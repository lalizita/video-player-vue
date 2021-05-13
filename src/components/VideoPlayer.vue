<template>
  <div class="video__container" @click="toggleVideoPlay">
    <video
      ref="videoPlayer"
      class="video__player"
      @loadedmetadata="updateVideoDetails"
      @timeupdate="updateVideoDetails"
    >
      <source src="../assets/video.mp4" />
    </video>
    <!--controls-->
    <div class="video__controls">
      <button class="video__controls__button" @click.stop="toggleVideoPlay">
        <v-icon style="color: #fff" v-if="isPlaying">mdi-pause</v-icon>
        <v-icon style="color: #fff" v-else>mdi-play</v-icon>
      </button>
      <div class="video__controls__volume">
        <button
          @click.stop="volumeOptionsOpen = !volumeOptionsOpen"
          ref="volumeTrack"
        >
          <v-icon v-if="volume === 0" style="color: #fff"
            >mdi-volume-low</v-icon
          >
          <v-icon v-else-if="volume > 0 && volume < 0.9" style="color: #fff"
            >mdi-volume-medium</v-icon
          >
          <v-icon v-else-if="volume >= 9" style="color: #fff"
            >mdi-volume-high</v-icon
          >
        </button>
        <div
          class="video__controls__volume--options"
          ref="videoVolumeTrack"
          @click.stop="handleVolumeClick"
        >
          <div class="video__controls__volume--track">
            <div
              class="video__controls__volume--track-current"
              :style="{ height: `${volume * 100}%` }"
            />
            <div
              class="video__controls__volume--track-ball"
              :style="{
                bottom: `Calc(${volume * 100}% - 0.25rem)`,
              }"
              draggable
              @dragend.stop="handleVolumeOnDrag"
            />
          </div>
        </div>
      </div>
      <div class="video__controls__duration">
        <span>{{ currentTimeFormatted }}</span>
        <span>{{ durationFormatted }}</span>
      </div>
      <div
        ref="videoPlayerTrack"
        class="video__controls__progress"
        @click.prevent.stop="handleTrackClick"
      >
        <div
          class="video__controls__progress__track"
          :style="{ width: progress + '%' }"
        >
          <div
            class="video__controls__progress__track--watching"
            :style="{ left: progress + '%' }"
            draggable
            @drag.stop.prevent="handleTrackOnDrag"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "VideoPlayer",
  data: () => ({
    volumeOptionsOpen: false,
    volume: 0.3,
    isPlaying: false,
    duration: 0,
    currentTime: 0,
  }),
  computed: {
    currentTimeFormatted() {
      return this.formatTime(this.currentTime);
    },
    durationFormatted() {
      return this.formatTime(this.duration);
    },
    progress() {
      return (this.currentTime / this.duration) * 100;
    },
  },
  mounted() {
    this.updateVideoDetails();
  },
  methods: {
    toggleVideoPlay() {
      if (this.$refs?.videoPlayer.paused) {
        this.isPlaying = true;
        this.$refs.videoPlayer.play();
      } else {
        this.isPlaying = false;
        this.$refs.videoPlayer.pause();
      }
    },
    handleVolumeClick(event) {
      const volume = this.$refs.videoVolumeTrack;
      const currentVolume = (volume.getBoundingClientRect().top - event.pageY + volume.offsetHeight) / 100;
      if (currentVolume > 1) {
        this.volume = currentVolume;
        this.$refs.videoPlayer.volume = 1;
      } else if (currentVolume < 0) {
        this.volume = currentVolume;
        this.$refs.videoPlayer.volume = 0;
      } else {
        this.volume = currentVolume;
        this.$refs.videoPlayer.volume = currentVolume;
      }
    },
    handleVolumeOnDrag(event) {
      const volume = this.$refs.videoVolumeTrack;
      const currentVolume = (volume.getBoundingClientRect().top - event.pageY + volume.offsetHeight) / 100;
      // const currentVolume =  event.layerY - volume.getBoundingClientRect().bottom;
      console.log({ currentVolume });
      if (currentVolume > 1) {
        this.volume = currentVolume;
        this.$refs.videoPlayer.volume = 1;
      } else if (currentVolume < 0) {
        this.volume = currentVolume;
        this.$refs.videoPlayer.volume = 0;
      } else {
        this.volume = currentVolume;
        this.$refs.videoPlayer.volume = currentVolume;
      }
    },
    updateVideoDetails() {
      if (this.$refs.videoPlayer) {
        this.duration = this.$refs.videoPlayer.duration;
        this.currentTime = this.$refs.videoPlayer.currentTime;
      }
    },
    formatTime(num) {
      let hours = Math.floor(num / 3600);
      let minutes = Math.floor((num % 3600) / 60);
      let seconds = Math.floor(num % 60);
      // Add leading zero if needed
      hours = hours < 10 ? "0" + hours : hours;
      minutes = minutes < 10 ? "0" + minutes : minutes;
      seconds = seconds < 10 ? "0" + seconds : seconds;
      // If hours > 0, return string with hours prepended
      if (hours > 0) {
        return hours + ":" + minutes + ":" + seconds;
      }
      return minutes + ":" + seconds;
    },
    handleTrackClick(event) {
      const currentTime =
        (this.duration * event.offsetX) /
        this.$refs.videoPlayerTrack.offsetWidth;
      this.currentTime = currentTime;
      this.$refs.videoPlayer.currentTime = currentTime;
    },
    handleTrackOnDrag(event) {
      console.log("DRAG EVENT");
      if (event.x !== 0 && event.y !== 0) {
        const track = this.$refs.videoPlayerTrack;
        if (track) {
          const leftMovement = event.pageX - track.getBoundingClientRect().left;
          let drag = 0;
          drag = leftMovement;
          if (leftMovement < 0) {
            drag = 0;
          } else if (leftMovement > track.offsetWidth) {
            drag = track.offsetWidth;
          }
          this.currentTime = this.duration * (drag / track.offsetWidth);
          this.$refs.videoPlayer.currentTime = this.currentTime;
        }
      }
    },
  },
};
</script>

<style>
.video__container {
  border-radius: 0.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  cursor: pointer;
  background: #000;
}
.video__player {
  width: 600px;
  margin: 0 auto;
}

.video__player source {
  height: 100%;
}

.video__controls {
  position: absolute;
  bottom: 5px;
  background: rgba(0, 0, 0, 0.75);
  width: 96%;
  padding: 5px;
  display: flex;
  z-index: 2;
  width: 100%;
  align-items: center;
  color: #fff;
}

.video__controls__volume {
  position: relative;
}

.video__controls__volume--options {
  position: absolute;
  height: 100px;
  padding: 5px;
  top: -110px;
  background: rgba(0, 0, 0, 0.75);
  border-radius: 0.25rem;
}

.video__controls__volume--track {
  position: relative;
  height: 100%;
  width: 4px;
  border-radius: 6px;
  background-color: #8c8c8c;
}

.video__controls__volume--track-current {
  background-color: #f17;
  position: absolute;
  bottom: 0;
  left: 0;
  width: 4px;
}

.video__controls__volume--track-ball {
  width: 20px;
  height: 0.9375rem;
  border-radius: 13px;
  position: absolute;
  background-color: #fff;
  border: 0.125rem solid #222;
  left: -7px;
}

.video__controls__duration {
  justify-self: flex-end;
}

.video__controls__progress {
  width: 100%;
  flex: 1;
  display: flex;
  align-items: center;
  padding: 0 0.45rem;
  background: #000;
  height: 0.25rem;
  position: relative;
}

.video__controls__progress__track {
  background: #fff;
  transition: width 0.2ms;
  display: flex;
  height: 0.25rem;
  border-radius: 6px;
}

.video__controls__progress__track--watching {
  height: 1.3rem;
  width: 0.5rem;
  border-radius: 20px;
  background: #fff;
  border: 1px solid #000;
  position: absolute;
  left: 0;
  bottom: -0.5rem;
}
</style>
