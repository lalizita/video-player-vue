<template>
  <div class="thumb" @click="$emit('click', thumbDetails.url)">
    <div class="thumb__video">
      <video @hover="autoplay = true" :autoplay="autoplay" muted loop>
        <source :src="thumbDetails.url" type="video/mp4" />
      </video>
    </div>
    <div class="thumb__title">{{ thumbDetails.name }}</div>
  </div>
</template>

<script>
export default {
  name: "VideoThumb",
  props: {
    thumbDetails: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      autoplay: false,
    };
  },
};
</script>

<style>
.thumb {
  width: 200px;
  height: 90px;
  margin: 8px 0px;
  display: flex;
  align-items: flex-start;
}

.thumb__video {
  background: #f43;
}

.thumb__video video {
  width: 100%;
  height: 100%;
}

.thumb__title {
  font-size: 16px;
  font-weight: bold;
  margin-left: 5px;
}
</style>
