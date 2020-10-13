<template>
  <section id="app" style="margin-top: 0; padding-top: 60px;">
    <div class="container" v-if="fileList">
      <div class="columns">
        <div class="column">
          <div class="level is-centered">
            <div class="level-item">
              <div class="content">
              <p class="title is-3">
                Happy Birthday!
              </p>
              <p class="subtitle is-6 is-italic">
                We love you today and always
              </p>
              </div>
            </div>
            <div class="level-item">
            </div>
            <div class="level-item">
              <figure class="image">
                <img ref="carousel" class="is-rounded" :src="'pics/' + imageIdx + '.jpg'">
              </figure>
            </div>
          </div>
        </div>
      </div>
      <div class="columns">
        <div class="column is-12 has-text-centered">
          <audio
            ref="player"
            controls
            :src="playingFile.src">
          </audio>
          <br/>
          <template v-if="playingFile.name">
            Playing "{{ playingFile.name }}"
          </template>
        </div>
      </div>
      <div class="columns is-multiline">
        <div class="column is-8 is-offset-2">
          <input type="text" v-model="textFilter" class="input" placeholder="Search">
        </div>
        <div class="column is-4" v-for="item in filteredList(fileList)">
          <div class="card">
            <div class="card-header">
              <p class="card-header-title">
                {{ filteredName(item) }}
              </p>
            </div>
            <div class="card-content">
              <img :src="item.url" v-if="item.url">
            </div>
            <footer class="card-footer">
              <a href="#" class="card-footer-item" @click="playSong(item)">Play</a>
            </footer>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      fileList: null,
      imageIdx: 1,
      textFilter: "",
      playingFile: {
        src: null,
        name: null
      }
    };
  },
  methods: {
    filteredList (list) {
      return list.filter((v) => v.name.toLowerCase().includes(this.textFilter.toLowerCase()))
                 .sort((a,b) => (a.name < b.name ? -1 : 1));
    },
    filteredName (item) {
      return item.name.slice(0, -4);
    },
    playSong (item) {
      this.playingFile = {
        src: "https://drive.google.com/uc?id=" + item.id,
        name: this.filteredName(item)
      }
      window.setTimeout(() => this.$refs.player.play(), 0);
    },
    incrementImageIdx () {
      this.imageIdx += 1;
      if (this.imageIdx > 6) {
        this.imageIdx = 1;
      }
    }
  },
  created () {
    fetch('filelist.json')
      .then(resp => resp.json())
      .then((data) => {
        this.fileList = data;
      });
  },
  mounted () {
    for ( let i = 1; i <= 6; i++ ) {
      let img = new Image();
      img.src = 'pics/' + i + '.jpg';
    }

    window.setInterval(() => {
      this.$refs.carousel.animate(
        [
          { opacity: 0 },
          { opacity: 1 },
          { opacity: 1 },
          { opacity: 1 },
          { opacity: 0 }
        ],
        {
          duration: 4000
        }
      );
      this.incrementImageIdx()
    }, 4000);
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

body {
  background-color: aliceblue;
}

figure.image {
  height: 250px;
  width: 250px;
}

img {
  object-fit: contain;
  max-height: 250px;
  background-color: black;
  display: inline-block;
  vertical-align: middle;
  height: 100% !important;
}
</style>
