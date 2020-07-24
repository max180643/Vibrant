<template>
  <div id="app">
    <h1>Vibrant</h1>

    <img id="preview" />

    <div>
      <div
        v-for="item in color"
        :key="item.id"
        class="colorList"
        v-clipboard:copy="item"
        v-clipboard:success="onCopy"
      >
        <div class="box" v-bind:style="{ background: item }"></div>
        <span v-bind:style="{ color: item }">{{ item }}</span>
      </div>
    </div>

    <input
      type="file"
      id="img"
      class="custom-file-input"
      name="img"
      accept="image/*"
      @change="previewFile()"
    />
  </div>
</template>

<script>
import * as Vibrant from "node-vibrant";

export default {
  name: "App",
  data: function() {
    return {
      color: [],
    };
  },
  mounted() {
    this.randomDemo();
  },
  methods: {
    getColor(image) {
      let data = this;
      data.color = [];
      Vibrant.from(image)
        .maxColorCount(64)
        .getPalette((err, palette) => {
          data.color.push(palette.DarkMuted.hex);
          data.color.push(palette.DarkVibrant.hex);
          data.color.push(palette.LightMuted.hex);
          data.color.push(palette.LightVibrant.hex);
          data.color.push(palette.Muted.hex);
          data.color.push(palette.Vibrant.hex);
        });
    },
    randomDemo() {
      const random = Math.floor(Math.random() * Math.floor(5)) + 1;
      const preview = document.getElementById("preview");
      preview.src = require(`@/assets/demo${random}.jpeg`);
      this.getColor(preview.src);
    },
    previewFile() {
      let preview = document.getElementById("preview");
      let file = document.querySelector("input[type=file]").files[0];
      let reader = new FileReader();
      let e = this;

      reader.onloadend = function() {
        preview.src = reader.result;
        e.getColor(preview);
      };

      if (file) {
        reader.readAsDataURL(file);
      } else {
        preview.src = "";
      }
    },
    onCopy(e) {
      const temp = e.trigger.lastChild.innerText;
      e.trigger.lastChild.classList.add("text-fade");
      e.trigger.lastChild.innerText = "Copied!";
      setTimeout(() => {
        e.trigger.lastChild.innerText = temp;
        e.trigger.lastChild.classList.remove("text-fade");
      }, 500);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.box {
  height: 50px;
  width: 50px;
  margin-left: auto;
  margin-right: auto;
}

h1 {
  margin-top: 2vh;
  margin-bottom: 2vh;
}

#preview {
  height: 50vh;
  max-width: 80vw;
  display: block;
  margin-left: auto;
  margin-right: auto;
}

.colorList {
  display: inline-block;
  margin: 10px;
  cursor: pointer;
}

.text-fade {
  animation-name: fade;
  animation-duration: 0.8s;
}

@keyframes fade {
  from {
    opacity: 0;
  }
  to {
    opacity: 100;
  }
}
</style>
