<template>
  <div id="app">
    <transition name='frame'>
      <div class="shader-container" v-show="ready">
        <iframe :height="frameHeight" frameborder="0" :src="embedSrc" allowfullscreen ref="if" ></iframe>
          <transition name='info'>
            <div class='shader-info' v-if="showShaderInfo">
              <h1> <i> {{ shader.name.toUpperCase() }} </i> </h1>  
              <h2> {{ shader.author }} </h2>  
              <p> {{ shader.desc }} </p>
            </div>
        </transition>
      </div>
    </transition>
  </div>
</template>

<script>
import Axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      key: 'ftrtwD',
      baseUrl: 'https://www.shadertoy.com/api/v1',
      shaders: [],
      ready: false,
      showShaderInfo: false,
      shader: {
        id: null,
        author: null,
        name: null,
        desc: null,
        data: null
      },
      showShaderInfoFor: 4000
    }
  },
  mounted() {
    this.getShader('shaderclub')
  },
  methods: {
    getShader(tag) {
      Axios.get(`${this.baseUrl}/shaders/query/${tag}?key=${this.key}`)
        .then( ({ data }) => {
          this.shaders = data.Results
          this.shader.id = this.shaders[Math.floor(Math.random() * this.shaders.length)]
          Axios.get(`${this.baseUrl}/shaders/${this.shader.id}?key=${this.key}`)
            .then( ({data}) => {
              let info = data.Shader.info
              this.shader.author = info.username
              this.shader.name = info.name
              this.shader.data = info.date
              this.shader.desc = info.description
              setTimeout( () => { 
                this.ready = true
                this.showShaderInfo = true
                setTimeout( () => {
                  this.showShaderInfo = false
                }, this.showShaderInfoFor )
              }, 200 )
            })
        })
    }
  },
  computed: {
    frameHeight() {
      return window.innerHeight + 60
    },
    embedSrc() {
      return `https://www.shadertoy.com/embed/${this.shader.id}?gui=false$t=10&paused=false&muted=false`
    }
  }
}
</script>

<style>
body {
  height: 100vh;
  overflow: hidden;
  margin: 0;
  border: 0;
  padding: 0;
  font-family: 'futura';
  text-align: center;
  color: #fdfdfd;
  font-weight: lighter;
}

iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  margin-top: -30px;
  z-index: -1;
}

h1 {
  font-size: 5em;
}

h2 {
  font-weight: lighter;
}

.shader-info {
  padding-top: 10%;
}

.frame-leave-active, .frame-enter-active, .info-leave-active, .info-enter-active {
  transition: opacity 1s;
}

.frame-enter, .frame-leave-to, .info-enter, .info-leave-to {
  opacity: 0;
}

</style>
