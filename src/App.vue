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
             class="h-96 aspect-video rounded-xl object-cover"></video>
    </div>

    <div class="w-full flex gap-10 justify-center mt-12 special-gothic-expanded-one-regular text-xl">
      <button v-on:click="submitAnswer('Ja')"
              class="w-1/3 max-w-40 hover:bg-emerald-400 cursor-pointer py-2 border-emerald-400 border-2 rounded-lg sm:bg-transparent bg-emerald-400">
        Ja
      </button>
      <button v-on:click="submitAnswer('Nein')"
              class="w-1/3 max-w-40 hover:bg-red-400 cursor-pointer py-2 border-red-400 border-2 rounded-lg sm:bg-transparent bg-red-400">
        Nein
      </button>
    </div>
  </div>

  <div class="absolute bottom-10 left-0 w-full flex justify-center" v-if="showToast">
    <div class="shadow-lg min-w-7/8 max-w-screen-sm flex justify-between items-center bg-amber-400 py-3 px-2 rounded-xl gap-10">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-6">
        <path fill-rule="evenodd" d="M9.401 3.003c1.155-2 4.043-2 5.197 0l7.355 12.748c1.154 2-.29 4.5-2.599 4.5H4.645c-2.309 0-3.752-2.5-2.598-4.5L9.4 3.003ZM12 8.25a.75.75 0 0 1 .75.75v3.75a.75.75 0 0 1-1.5 0V9a.75.75 0 0 1 .75-.75Zm0 8.25a.75.75 0 1 0 0-1.5.75.75 0 0 0 0 1.5Z" clip-rule="evenodd" />
      </svg>
      <h1 style="font-family: Inter, Helvetica, sans-serif" class="text-center">{{ error }}</h1>
      <svg v-on:click="closeToast()" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-6 cursor-pointer hover:bg-amber-300 rounded-full">
        <path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 0 1 1.06 0L12 10.94l5.47-5.47a.75.75 0 1 1 1.06 1.06L13.06 12l5.47 5.47a.75.75 0 1 1-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 0 1-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 0 1 0-1.06Z" clip-rule="evenodd" />
      </svg>
    </div>
  </div>

  <div v-if="submitted" class="absolute top-0 left-0 h-full w-full h-full flex flex-col items-center justify-center px-5" style="background-color: #ae7b82;">
    <img :src="getImgSrc()">
  </div>

  <Loader v-if="submitting"></Loader>

</template>

<style scoped>

</style>

<script>
import axios from 'axios';
import ChildComponent from './Loader.vue'
import Loader from "@/Loader.vue";

export default {
  mounted() {
    let answerAlreadySubmitted = localStorage.getItem("answer");
    if(answerAlreadySubmitted) {
      this.answer = answerAlreadySubmitted;
      this.submitted = true;
    }
  },
  components: {Loader},
  data() {
    return {
      showVideo: false,
      submitting: false,
      submitted: false,
      answer: null,
      showToast: false,
      error: "Es ist etwas schiefgelaufen"
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
    getImgSrc() {
      return this.answer === "Ja" ? "/png-flork.png" : "/florkSad.png"
    },
    async submitAnswer(option) {
      try {
        this.answer = option;
        this.submitting = true;
        const response = await axios.post("http://localhost:3000/decision", {answer: option},);
        localStorage.setItem("answer", option);
        this.submitted = true;
      } catch (error) {
        console.log(error);
        this.displayToast(error.message);
      }
      this.submitting = false;
    },
    closeToast() {
      this.showToast = false;
      this.error = null;
    },
    displayToast(errorMsg) {
      this.error = errorMsg;
      this.showToast = true;
      setTimeout(() => {
        this.showToast = false;
      }, 2500);
    }
  }
}
</script>