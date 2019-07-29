<template>
  <div id="app">
      
        <FreehandDrawSVG
          v-bind:title="drawProps.title"
          v-bind:colors="drawProps.colors"
          v-bind:bgColors="drawProps.bgColors"
          v-bind:lineColor = "drawProps.lineColor"
          v-bind:bgColor = "drawProps.bgColor"
          v-on:changeLineColor = "changeLineColor"
          v-on:changeBgColor = "changeBgColor"
          v-on:drawSubmit="saveDrawing"
          >
        </FreehandDrawSVG>

  </div>
</template>

<script>
import FreehandDrawSVG from './components/FreehandDrawSVG.vue'

export default {
  name: 'app',

  components: {
    FreehandDrawSVG
  },

  data() {
    return {
      drawProps: 
        {
          title: "Freehand SVG Draw",
          colors: ['#EAEAEA','#292929', '#00A0FF', '#FF29AA', '#FFE500', '#FF3030', '#12EB0A'],
          bgColors: ['#EAEAEA','#292929', '#00A0FF', '#FF29AA', '#FFE500', '#FF3030', '#12EB0A'],
          lineColor: "#292929",
          bgColor: "#EAEAEA"
        }
      
    }
  },

  methods: {
    changeLineColor: function(color){
      this.drawProps.lineColor = color;
    },

    changeBgColor: function(color){
      this.drawProps.bgColor = color;
    },

    saveDrawing: function(drawSvg){
      console.log (drawSvg)      
    }
  },

  mounted: function() {
  }


}
</script>

<style>
body, html{
  font-family: 'Helvetica Neue', Helvetica, arial, sans-serif;
  background: #131415;
  margin: 0;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  min-height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
}

</style>
