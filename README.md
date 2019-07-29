# freehand-svg-draw
Draw with mouse or fingers vector SVG paths. 

- Basic functionality, just colors and fixed brush width
- Minimal user interface with undo, clearn and download buttons
- Works fine on mobile and desktop
- Canvas size defined with css

Made for VueJS. 

![Svg pencil Draw Vuejs in browser](https://artrayd.com/freehand-svg/freehand-svg-ui.png)

# Demo
https://artrayd.com/freehand-svg/

### Based on tomhodgins gist
https://gist.github.com/tomhodgins/bffcc0dce83f2d593afc

### If all you need is just component:
https://gist.github.com/artrayd/989fdb9718aaffa16a4346aaa89c9bac


## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Run your tests
```
npm run test
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

### Known problems
- When cursor comes out of canvas container, it throws error - but it doesn't affect functionality
- If not scrolled to the top, mouse cursor distance is not calculated correctly and trace path appears in the wrong place
