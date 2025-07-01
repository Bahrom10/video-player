<template>
  <div class="video-player" @mousemove="handleMouseMove" @mouseleave="handleMouseLeave"
  @keydown="handleKeyDown" ref="VideoParentObject" @contextmenu.prevent tabindex="0">
    <video
      class="video-player-video"
      ref="VideoEl"
      @click="togglePlay"
      @timeupdate="updateCurrentTime"
      @loadedmetadata="updateDuration"
      :src="VideoSrc"
      :autoplay="AutoPlay"
      :loop="Loop"
      :playbackRate="PlaybackSpeed"
      :muted="Muted"
    ></video>
    <div
      class="video-player-bottom"
      :class="{ inactive: !mouseActive && !isHovered }"
      :style="`max-width: ${width.value * 1.2}px`"
      ref="VideoPlayerBottom"
      @mouseenter="handleHoverEnter"
      @mouseleave="handleHoverLeave"
    >
      <button class="video-player-bottom-play" @click="togglePlay">
        <img :src="imagePath" alt="" width="48" height="48" />
      </button>
      <div class="video-player-bottom-timeline">
        <span class="video-player-bottom-timeline-current">{{ formatCurrentTime }}</span>
        <input
          type="range"
          step="1"
          class="video-player-bottom-timeline-range"
          min="0"
          :max="duration"
          @input="handleInput"
          :value="currentTime"
        />
        <span class="video-player-bottom-timeline-duration">{{ formatDuration }}</span>
      </div>
      <button class="video-player-bottom-volume" @click="toggleVolume">
        <img src="../src/assets/img/volume.svg" alt="" ref="volumeImg" width="48" height="48"/>
        <div class="video-player-bottom-volume-cross" ref="cross"></div>
        <div class="wrapper" ref="innerWrapper">
          <input
            type="range"
            step="0.01"
            class="video-player-bottom-volume-range"
            min="0"
            max="1"
            :value="VideoEl ? VideoEl.volume : 1"
            @input="handleVolumeInput"
            @click="(event) => event.stopPropagation()"
          />
        </div>
      </button>
      <div class="wrapper outer" ref="outerWrapper">
          <input
            type="range"
            step="0.01"
            class="video-player-bottom-volume-range"
            min="0"
            max="1"
            :value="VideoEl ? VideoEl.volume : 1"
            @input="handleVolumeInput"
            @click="(event) => event.stopPropagation()"
          />
        </div>
      <button class="video-player-bottom-settings">
        <img src="./assets/img/settings.svg" alt="">
        <div class="settings">
            <div class="inner">
                <button @click="()=> controll('speed')">Speed</button>
            <button @click="()=> controll('quality')" >Quality</button>
            <button disabled @click="() => controll('sub')">Subtitles</button disabled>
            <button @click="enterPiP">Picture-in-Picture</button>
            <button @click="shareVideo">Share</button>
            <div class="video-player-bottom-settings-speed" ref="speedControll">
                <button @click="setVideoSpeed(0.25)">0.25x</button>
                <button @click="setVideoSpeed(0.5)">0.5x</button>
                <button @click="setVideoSpeed(1)">1x</button>
                <button @click="setVideoSpeed(1.5)">1.5x</button>
                <button @click="setVideoSpeed(2)">2x</button>
            </div>
            <div class="video-player-bottom-settings-quality" ref="qualityControll">
                <button>Auto</button>
                <button v-for="[key,value] in Object.entries(props.qualityUrls)" :key="key" @click="setUrl(value)">{{ key }}</button>
            </div>
            <div class="video-player-bottom-settings-sub" ref="subControll">
                <button>English</button>
                <button>Spanish</button>
                <button>French</button>
                <button>German</button>
                <button>Off</button>
            </div>
            </div>
        </div>
      </button>
      <button class="video-player-bottom-fullscreen" @click="toggleFullscreen">
        <img src="../src/assets/img/fullscreen.svg" alt="" />
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onUnmounted, computed, onMounted } from 'vue';

const props = defineProps({
  Name: {
    type: String,
    required: true,
  },
  VideoSrc: {
    type: String,
    required: true,
  },
  AutoPlay: {
    type: Boolean,
    default: false,
  },
  Loop: {
    type: Boolean,
    default: false,
  },
  PlaybackSpeed: {
    type: Number,
    default: 1,
  },
  Play: {
    type: Function,
    default: () => {},
  },
  Pause: {
    type: Function,
    default: () => {},
  },
  VolumeChange: {
    type: Function,
    default: () => {},
  },
  FullscreenToggle: {
    type: Function,
    default: () => {},
  },
  Volume: {
    type: Number,
    default: 1,
  },
  Muted: {
    type: Boolean,
    default: false,
  },
  qualityUrls: {
    type: Object,
    default: {},
  },
  VideoDOM: {
    type: Object,
    default: ref(null),
  }
})
const VideoEl = ref(null);
const currentTime = ref(0);
const duration = ref(0);
const imagePath = ref("../src/assets/img/play.svg");
const mouseActive = ref(true);
const isHovered = ref(false); 
let inactivityTimeout = null;
const isFullscreen = ref(false);
const VideoParentObject = ref(null);
const VideoPlayerBottom = ref(null); 
const cross = ref(null);
const innerWrapper = ref(null);
const outerWrapper = ref(null);
const speedControll = ref(null);
const qualityControll = ref(null);
const subControll = ref(null);

const controll = (name) => {
    
    if(name === 'speed'){
        speedControll.value.style.display = "block";
        qualityControll.value.style.display = "none";
        subControll.value.style.display = "none";
    }else if(name === 'quality'){
        speedControll.value.style.display = "none";
        qualityControll.value.style.display = "block";
        subControll.value.style.display = "none";
    }else if(true){
        speedControll.value.style.display = "none";
        qualityControll.value.style.display = "none";
        subControll.value.style.display = "block";
    }    console.log(name);

}
const setUrl = (url) => {
    VideoEl.value.src = url;
    VideoEl.value.play();
}

const handleKeyDown = (event) => {  
  switch (event.key) {
    case " ":

      togglePlay();
      break;
    case "m":
      toggleVolume();
      break;
    case "f":
      toggleFullscreen();
      break;
    case "ArrowRight":
      VideoEl.value.currentTime += 5;
      break;
    case "ArrowLeft":
      VideoEl.value.currentTime -= 5;
      break;
    case "ArrowUp":
      VideoEl.value.volume = VideoEl.value.volume === 1 ? 1 : VideoEl.value.volume + 0.1;
      break;
    case "ArrowDown":
      VideoEl.value.volume = VideoEl.value.volume === 0 ? 0 : VideoEl.value.volume - 0.1;
      break;
    default:
    console.log(event.key);
    break;
  }
}

onMounted(() => {
  if (VideoEl.value.paused) {
    imagePath.value = "../src/assets/img/stop.svg";
  } else {
    imagePath.value = "../src/assets/img/play.svg";
  }
  if(VideoParentObject.value.offsetWidth < 1024){
    outerWrapper.value.style.display = "none";
  }else{
    innerWrapper.value.style.display = "none";
  }
    speedControll.value.style.display = "none";
    qualityControll.value.style.display = "none";
    subControll.value.style.display = "none";
    props.VideoDOM.value = VideoEl.value
});

onUnmounted(() => {
  if (inactivityTimeout) clearTimeout(inactivityTimeout);
});

const enterPiP = async () => {
};

const togglePlay = () => {
  if (VideoEl.value.paused) {
    imagePath.value = "../src/assets/img/stop.svg";
    VideoEl.value.play();
  } else {
    imagePath.value = "../src/assets/img/play.svg";
    VideoEl.value.pause();
  }
};

const updateCurrentTime = () => {
  currentTime.value = VideoEl.value.currentTime;
};

const handleInput = (event) => {
  currentTime.value = event.target.value;
  VideoEl.value.currentTime = currentTime.value;
};

const updateDuration = () => {
  duration.value = VideoEl.value.duration;
};

const formatTime = (time) => {
  const minutes = Math.floor(time / 60);
  const seconds = Math.floor(time % 60).toString().padStart(2, "0");
  return `${minutes}:${seconds}`;
};

const formatCurrentTime = computed(() => formatTime(currentTime.value));
const formatDuration = computed(() => formatTime(duration.value));

const width = computed(() => (VideoEl.value ? VideoEl.value.offsetWidth : 0));

const handleMouseMove = () => {
  mouseActive.value = true;

  if (inactivityTimeout) clearTimeout(inactivityTimeout);

  inactivityTimeout = setTimeout(() => {
    mouseActive.value = false;
  }, 1000);
};

const handleMouseLeave = () => {
  mouseActive.value = false;
};

onUnmounted(() => {
  if (inactivityTimeout) clearTimeout(inactivityTimeout);
});

const handleVolumeInput = (event) => {
  VideoEl.value.volume = event.target.value;
  cross.value.classList.remove("muted");
  VideoEl.value.muted = false
};

const toggleVolume = () => {
  VideoEl.value.muted = !VideoEl.value.muted;
  cross.value.classList.toggle("muted");
};

const toggleFullscreen = () => {
  const parent = VideoParentObject.value;

  if (!document.fullscreenElement) {
    if (parent.requestFullscreen) {
      parent.requestFullscreen();
    } else if (parent.webkitRequestFullscreen) {
      parent.webkitRequestFullscreen();
    } else if (parent.mozRequestFullScreen) { 
      parent.mozRequestFullScreen();
    } else if (parent.msRequestFullscreen) { 
      parent.msRequestFullscreen();
    }
    isFullscreen.value = true;
  } else {
    if (document.exitFullscreen) {
      document.exitFullscreen();
    } else if (document.webkitExitFullscreen) { 
      document.webkitExitFullscreen();
    } else if (document.mozCancelFullScreen) { 
      document.mozCancelFullScreen();
    } else if (document.msExitFullscreen) { 
      document.msExitFullscreen();
    }
    isFullscreen.value = false;
  }
};

const handleHoverEnter = () => {
  isHovered.value = true;
};

const handleHoverLeave = () => {
  isHovered.value = false;
};
const setVideoSpeed = (speedRate) => {
  VideoEl.value.playbackRate = speedRate;
};
</script>
