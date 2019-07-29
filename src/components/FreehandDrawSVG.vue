<template>
  <div class="draw">

    <div class="toolbar" :style="{ right: toolBarRight }">


      <div class="allColors">

        <div class="panelUp mobile">
          
        </div>

        <transition-group class="lineColor" name="color-list" tag="ul" >
          <li v-for="(color, $index) in colors" v-bind:style='{ "background-color": color }' v-on:click="changeLineColor(color, $index)" v-bind:key="color"></li>
        </transition-group>


        <transition-group class="bgColor" name="color-list" tag="ul" >
          <li v-for="(color, $index) in bgColors" v-bind:key="color" v-bind:style='{ "background-color": color }' @click="changeBg(color, $index)"></li>
        </transition-group>

      </div>

    </div>

<!--     <ul class="toolbar" v-bind:style='{ "background-color": bgColor }'>
      <li v-for="color in colors" v-bind:style='{ "background-color": color }' v-on:click="changeLineColor(color)" v-bind:key="color"></li>
      <li class="bgColor" v-bind:style='{ "background-color": bgColor }' @click="changeBg()" ></li>
    </ul> -->

    <div class="buttons"  v-bind:class="{ showButtons: undo }" >
      <button v-on:click="download()" class="btn submitBtn"> <img src="../assets/download.svg" alt="Download"> </button>
      <button v-on:click="clean()" class="btn clearBtn"> <img src="../assets/clear.svg" alt="Clear Canvas"> </button>
      <button v-on:click="undoLine()" class="btn undoBtn" > <img src="../assets/undo.svg" alt="Undo"> </button>
    </div>
    
    <div class="canvas">
      
      <h2 v-bind:style='{ "color": lineColor }' class="noselect" v-if="!undo"> {{ title }} </h2>

      <!-- Canvas size is defined in CSS, search for ".canvas" -->

      <svg xmlns=http://www.w3.org/2000/svg version="1.1" class="drawSvg" :width="canvasWidth" :height="canvasHeight"
        @mousedown="linestart()"
        @touchstart="linestart()"
        @mousemove="lineMove()"
        @touchmove="lineMove()"
        @mouseup="lineEnd()"
        @touchend="lineEnd()"
        @onmouseleave="outOfCanvas()"
        @touchcancel="outOfCanvas()"
        >
        
        <rect id="bg" width="100%" height="100%" v-bind:fill="bgColor"></rect>
      
      </svg>

      <div id="cursor" v-bind:style='{ "background-color": lineColor }'></div>

    </div>

    

  </div>
</template>

<script>

require('../assets/css/freehandDraw.css')

// hello jquery
const $ = document.querySelector.bind(document);
const $$ = document.querySelectorAll.bind(document);

  export default {
    name: 'Draw',
    props: [
      'title',
      'colors',
      'bgColors',
      'lineColor',
      'bgColor',
    ],
    data () {
      return {
          board: '',
          cursor: '',
          colorNum: 0,
          gesture: false,
          line: '',
          radius: 2.5,
          width: 8,
          undo: false,
          onCanvas: false // mouseout event is not firing, dunno why
        }
      },

    computed: {

      canvasWidth: function(){
        return this.board.clientWidth
      },

      canvasHeight: function(){
        return this.board.clientHeight
      },

      toolBarRight:{
        get:  function(){
          return -this.colors.length * 51.5 + 'px'  
        },

        set: function(newVal){
          // this.toolBarRight = newVal // This is more right way to do that, but it won't work( Did the same with css hover
        }
        
      }

      // requuires some setters, I don't understand it.

      // rect: function(){
      //   return this.board.getBoundingClientRect();
      // },

      // cursorX: function(){
      //   let rect = this.board.getBoundingClientRect();
      //   return Math.round( event.clientX - this.rect.x ) || Math.round(event.changedTouches[0].clientX - this.rect.x)
      // },

      // cursorY: function(){
      //   return Math.round( event.clientY - this.rect.y )  || Math.round(event.changedTouches[0].clientY - this.rect.y)
      // }

    },

    methods:{

      initBoard: function(){
        this.board = $('.drawSvg')
        this.cursor = $('#cursor')
        this.gesture = false

      },

      linestart: function(){

        this.undo = true;

        let e = event

        // this.line += 'M' + this.cursorX + ',' + this.cursorY 
        // I prefer to use code above with computed valuse, but it drops some errors about setter, and I didn't succeed to solve it in 15 minutes.


        let rect = this.board.getBoundingClientRect();
        let cursorX = Math.round( e.clientX - rect.x ) || Math.round(e.changedTouches[0].clientX - rect.x)
        let cursorY = Math.round( e.clientY - rect.y )  || Math.round(e.changedTouches[0].clientY - rect.y)

        this.line += 'M' + cursorX + ',' + cursorY 

        this.cursor.style.opacity = 1
        this.gesture = true
        e.preventDefault()
      },

      lineMove: function(){

        let e = event
        let rect = this.board.getBoundingClientRect();

        let cursorX = Math.round( e.clientX - rect.x ) || Math.round(e.changedTouches[0].clientX - rect.x)
        let cursorY = Math.round( e.clientY - rect.y )  || Math.round(e.changedTouches[0].clientY - rect.y)


        if ( this.gesture == true ){
          this.line += 'L' + cursorX + ',' + cursorY
          // this.line += 'L'+(e.clientX||e.touches[0].clientX)+','+(e.clientY||e.touches[0].clientY)+' '
          this.trace((e.clientX||e.touches[0].clientX), (e.clientY||e.touches[0].clientY))
        }

        this.cursor.style.top =  e.clientY - rect.y - this.radius+'px'
        this.cursor.style.left = e.clientX - rect.x - this.radius+'px'

        this.cursorX = e.clientX - rect.x
        this.cursorY = e.clientY - rect.y

        this.onCanvas = true
      },

      trace: function(x,y){
          let dot = document.createElement('div');
          dot.classList.add('dot')
          dot.style.top = y-this.radius+'px';
          dot.style.left = x-this.radius+'px';
          dot.style.background = this.lineColor;
          dot.style.width = dot.style.height = this.radius*2+'px';
          document.body.appendChild(dot);
          setTimeout(function(){dot.style.opacity=0},500);
          setTimeout(function(){document.body.removeChild(dot)},1000);
      },

      lineEnd: function(){
        
        let e = event;
        let rect = this.board.getBoundingClientRect();

        let cursorX = Math.round( e.clientX - rect.x ) || Math.round(e.changedTouches[0].clientX - rect.x);
        let cursorY = Math.round( e.clientY - rect.y ) || Math.round(e.changedTouches[0].clientY - rect.y);


        this.line += 'L' + cursorX + ',' + cursorY;
        // this.line += 'L'+(e.clientX||e.changedTouches[0].clientX)+','+(e.clientY||e.changedTouches[0].clientY)
        this.cursor.style.opacity = .5
        let path = document.createElementNS('http://www.w3.org/2000/svg','path');
        path.setAttributeNS(null,'d',this.line);
        path.setAttributeNS(null,'fill','none');
        path.setAttributeNS(null,'stroke-linecap','round');
        path.setAttributeNS(null,'stroke-linejoin','round');
        path.setAttributeNS(null,'stroke', this.lineColor);

        path.setAttributeNS(null,'stroke-width', this.width);
        this.board.appendChild(path);
        // this.board.innerHTML = this.board.innerHTML // force SVG repaint after DOM change
        this.gesture = false;
        this.line = '';


        // saving to local storage
        // localStorage.svg = new XMLSerializer().serializeToString(this.board)

        
      },

      // For changing color positions inside toolbar
      arrayMove: function (arr, fromIndex, toIndex) {
          var element = arr[fromIndex];
          arr.splice(fromIndex, 1);
          arr.splice(toIndex, 0, element);
      },


      rgb2hex: function(rgb) { 
           if (  rgb.search("rgb") == -1 ) {
                return rgb;
           } else {
                rgb = rgb.match(/^rgba?\((\d+),\s*(\d+),\s*(\d+)(?:,\s*(\d+))?\)$/);
                function hex(x) {
                     return ("0" + parseInt(x).toString(16)).slice(-2);
                }
                return "#" + hex(rgb[1]) + hex(rgb[2]) + hex(rgb[3]); 
           }
      },

      setActiveColor: function(Li){
        let allColors = $$(Li)
        let target = event.target // for set time out

        for (let color of allColors) {
          color.classList.remove('activeColor') 
        }

        setTimeout (function(){
          target.classList.add('activeColor')
        }, 10)
      },

      setActiveColorMounted: function(li, arr, color){

        let allLi = $$(li)
        let colorIndex = arr.indexOf(color)

        for (let li of allLi) {
          let liColor = this.rgb2hex(li.style.backgroundColor).toUpperCase()

            if( liColor === color){
              li.classList.add('activeColor')

              this.arrayMove( arr, colorIndex, 0 )
            }
        }
      },

      changeLineColor(color, index){
        window.scrollTo({top: 0, behavior: 'smooth'});

        this.$emit( 'changeLineColor', color )

        this.setActiveColor('.lineColor li')

        // changing circle position
        this.arrayMove( this.colors, index, 0 )
      },

      changeBg(color, index){ // just change backgrounds colors one after another
        window.scrollTo({top: 0, behavior: 'smooth'});

        this.$emit( 'changeBgColor', color )

        this.setActiveColor('.bgColor li')

        $('.drawSvg #bg').setAttribute('fill', this.bgColor )

        this.arrayMove( this.bgColors, index, 0 )

        // this.board.innerHTML = this.board.innerHTML // force SVG repaint after DOM change

      },

      undoLine: function(){
        let paths = $$('.drawSvg path');
        $('.drawSvg').removeChild(paths[paths.length-1])
      },

      clean: function (){
        this.line = '';
        this.undo = false;

        let paths = $$('.drawSvg path').length
        
        let i = 0

        for (i=0;i<paths;i++){
          this.board.removeChild(this.board.querySelectorAll('path')[0])
        }

        // this.board.innerHTML = ''; more effective, but removes bg also

      },

      download: function(){
        var dl = document.createElement("a");

        let drawing = this.svgDataURL( $('.drawSvg') )

        console.log (drawing)

        document.body.appendChild(dl); // This line makes it work in Firefox.
        dl.setAttribute("href", drawing)
        dl.setAttribute("download", "freehand-svg-drawing.svg");
        dl.click();
      },


      svgDataURL: function(){
        // let drawSvg = new XMLSerializer().serializeToString(this.board)
        let serialSvg = (new XMLSerializer).serializeToString(this.board);
        return "data:image/svg+xml," + encodeURIComponent(serialSvg);

        

        // this.$emit('drawSubmit', drawSvg);
      },


    },

    mounted: function () {
      this.initBoard()

      this.colors

      this.setActiveColorMounted('.lineColor li', this.colors, this.lineColor) 
      this.setActiveColorMounted('.bgColor li', this.bgColors, this.bgColor)


    },

  }
</script>

