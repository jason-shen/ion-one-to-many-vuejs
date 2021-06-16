<template>
  <header class="flex h-16 justify-center items-center text-xl bg-black text-white">
    <h1>VueJS Pion/ion One to Many Video Call</h1>
    <div class="absolute top-2 right-5">
    <button id="bnt_pubcam" class="bg-blue-500 px-4 py-2 text-white rounded-lg mr-5" @click="start(true)" v-if="this.$root.publisher">Publish Camera</button>
    <button id="bnt_pubscreen" class="bg-green-500 px-4 py-2 text-white rounded-lg" @click="start(false)" v-if="this.$root.publisher">Publish Screen</button>
    </div>
  </header>
</template>

<script>
  export default {
    name: "Header",
    methods: {
      start(e) {
        this.$root.startPublish(e);
      }
    }
  }
</script>
