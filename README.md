# Vue 3 + Vite

This is simple video-player. The purpose of this project was just adding that to my portfolio, but i had a bit of plans to make it an npm package. This player works on Vue 3. But soon i will rewrite this code into React and pure HTML with JS.

## Project setup

There are no dependencies for this project, so you will have to install only this package.

## Props to this components

Here is a list of props

{
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

# Functions bellow, will be called, when the following action happens. For example, when video is played, either paused, this actions will happen.

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

# These are props-configuration for the video HTML-tag

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

# This is the prop to get the video HTML-DOM object.

  VideoDOM: {
    type: Object,
    default: ref(null),
  }
}