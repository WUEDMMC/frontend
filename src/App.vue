<template>
  <div class="h-full flex flex-col justify-center">
    <div class="w-full flex flex-col items-center gap-10">
      <h1 class="text-3xl text-center special-gothic-expanded-one-regular">Willsch uf es Date mit mir?</h1>
      <img src="/hero.jpg" class="h-96 aspect-video object-cover object-center rounded-xl" alt="hero"
           v-on:click="displayVideo()">
    </div>

    <div v-if="showVideo" v-on:click="displayVideo()"
         class="absolute h-full w-full left-0 top-0 flex justify-center px-5 items-center"
         style="background-color: rgba(0,0,0, 0.7)">
      <video ref="video" src="/video.mp4" controls
             class="h-96 aspect-video rounded-xl object-cover -translate-y-[10px]"></video>
    </div>

    <div class="w-full flex gap-10 justify-center mt-12 special-gothic-expanded-one-regular text-xl">
      <button v-on:click="submitAnswer('Nein')"
              class="w-1/3 max-w-40 hover:bg-emerald-400 cursor-pointer py-2 border-emerald-400 border-2 rounded-lg sm:bg-transparent bg-emerald-400">
        Ja
      </button>
      <button v-on:click="submitAnswer('Ja')"
              class="w-1/3 max-w-40 hover:bg-red-400 cursor-pointer py-2 border-red-400 border-2 rounded-lg sm:bg-transparent bg-red-400">
        Nein
      </button>
    </div>
  </div>

</template>

<style scoped>

</style>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      showVideo: false,
      submitting: false,
      answer: false
    }
  },
  methods: {
    displayVideo() {
      if (this.showVideo) {
        this.$refs.video.pause();
      } else {
        setTimeout(() => {
          this.$refs.video.play();
        }, 500);
      }
      this.showVideo = !this.showVideo;
    },
    async submitAnswer(option) {
      try {
        this.submitting = true;
        const response = await axios.post("http://localhost:3000/send-mail", {answer: option},);
        console.log(response);
      } catch (error) {
        console.log(error);
      }
      this.submitting = false;
    }
  }
}
</script>