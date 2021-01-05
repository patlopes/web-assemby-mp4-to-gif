<template>
  <div class="container">
    <div class="reproduction-container">
      <video :src="videoURL" controls autoplay muted></video>
    </div>
    <input id="video-input" type="file" accept=".mp4" v-on:input="loadVideo" />
    <label v-if="video === null" for="video-input" class="button-container"
      >Selecione um video</label
    >
    <div class="button-container" v-else v-on:click="makeGIF">
      {{ message }}
    </div>
    <div class="reproduction-container">
      <img :src="gif" alt="gif" v-if="gif !== null" />
    </div>
  </div>
</template>

<script>
import { createFFmpeg, fetchFile } from "@ffmpeg/ffmpeg";
const ffmpeg = createFFmpeg({ log: false });

export default {
  name: "Mp4ToGif",
  data: () => ({
    video: null,
    videoURL: null,
    gif: null,
    loading: null,
    message: "Converter video para GIF",
  }),
  methods: {
    async loadVideo(e) {
      this.video = e.target.files?.item(0);
      this.videoURL = URL.createObjectURL(this.video);
    },
    async makeGIF() {
      this.message = "Carregando FFMPEG...";
      await this.loading;
      this.message = "Carregando o video na memoria...";
      ffmpeg.FS("writeFile", "video.mp4", await fetchFile(this.video));

      this.message = "Convertendo o video para GIF...";
      await ffmpeg.run(
        "-i",
        "video.mp4",
        "-t",
        "6.0",
        "-ss",
        "0.0",
        "-f",
        "gif",
        "out.gif"
      );

      // Read the result
      this.message = "Lendo o GIF da memoria...";
      const data = ffmpeg.FS("readFile", "out.gif");

      // Create a URL
      const url = URL.createObjectURL(
        new Blob([data.buffer], { type: "image/gif" })
      );
      this.gif = url;

      this.message = "Converter video para GIF";
      this.video = null;
    },
  },
  /* eslint-disable */
  async beforeMount() {
    this.loading = ffmpeg.load();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
input {
  display: none;
}

.container {
  height: 100vh;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
}

.reproduction-container {
  width: 30vw;
  height: 18vw;
  left: 495px;
  top: 28px;

  display: flex;
  justify-content: center;
  align-items: center;

  background: #6e6f80;
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
  border-radius: 15px;
}

.reproduction-container > * {
  min-width: 90%;
  max-width: 90%;

  box-shadow: inset 4px 4px 3px 1px rgba(0, 0, 0, 0.45);
  border-radius: 9px;
}

.button-container {
  width: 30vw;
  height: 41px;
  left: 495px;
  top: 409px;

  display: flex;

  justify-content: center;
  align-items: center;

  background: #7278cc;
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
  border-radius: 15px;

  font-family: Roboto;
  font-style: normal;
  font-weight: bold;
  font-size: 24px;
  line-height: 28px;

  color: #f2f3ff;

  cursor: pointer;
}
</style>
