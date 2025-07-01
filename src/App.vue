<template>

  <VideoPlayer
    VideoSrc="http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ElephantsDream.mp4"
    :AutoPlay="true"
    :Loop="false"
    :PlaybackSpeed="1"
    :Volume="0.5"
    :Muted="false"
    Name=""
    :VideoDOM="dom"
  />
</template>

<script setup>
import { ref } from 'vue';
import VideoPlayer from './VideoPlayer.vue';

const dom = {}


</script>
