<template>
  <div class="flex flex-col h-screen">
    <Header />
    <VideoCall ref="videoElements" />
  </div>
</template>

<script>
import Header from './components/Header.vue';
import VideoCall from './components/VideoCall.vue';

import { LocalStream, Client } from 'ion-sdk-js';
import { IonSFUJSONRPCSignal } from 'ion-sdk-js/lib/signal/json-rpc-impl';
let isPub , client;
// http://localhost:8080/?pulish=true as publisher
// http://localhost:8080 as subscriber

const URL = new URLSearchParams(window.location.search).get("publish");
  console.log("url", URL);
  if (URL) {
    isPub = true;
  } else {
    isPub =false;
  }

  const config = {
    iceServers: [
      {
        urls: "stun:stun.l.google.com:19302",
      },
    ],
  };

export default {
  created() {
    const signal = new IonSFUJSONRPCSignal("ws://localhost:7000/ws");
    client = new Client(signal, config);
    signal.onopen = () => client.join("test room");
    if (!isPub) {
      client.ontrack = (track, stream) => {
        console.log("got track: ", track.id, "for stream: ", stream.id);
        track.onunmute = () => {
          console.log("unmute")
          this.$refs.videoElements.$refs.sub_video.srcObject = stream;
          this.$refs.videoElements.$refs.sub_video.autoplay = true;
          this.$refs.videoElements.$refs.sub_video.muted = false;

          // when the publisher leave
          stream.onremovetrack = () => {
            this.$refs.videoElements.$refs.sub_video.srcObject = null;
          }
        }
      }
    }
  },
  data() {
    return {
      publisher: isPub
    }
  },
  methods: {
    startPublish(type) {
      if (type) {
        LocalStream.getUserMedia({
          resolution: "vga",
          audio: true,
          codec: "vp8"
        }).then((stream) => {
          this.$refs.videoElements.$refs.pub_video.srcObject = stream;
          client.publish(stream);
        }).catch(console.error);
      } else {
        LocalStream.getDisplayMedia({
          resolution: "vga",
          video: true,
          audio: true,
          codec: "vp8"
        }).then((stream) => {
          this.$refs.videoElements.$refs.pub_video.srcObject = stream;
          client.publish(stream);
        }).catch(console.error);
      }
    }
  },
  name: 'App',
  components: {
    Header,
    VideoCall
  }
}
</script>

<style>
@import "https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css";
</style>
