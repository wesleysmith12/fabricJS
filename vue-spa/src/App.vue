<template>
  <div id="app" style="margin-top: 0">
    <div style="text-align: center">Canvas Size: {{ canvasSize }} &emsp; Bounds Size: {{ boundsSize }}</div>
    <br>
    <canvas id="c">Your browser does not support the HTML5 canvas element.</canvas>
  </div>
</template>

<script>
import Fabric from "fabric";
export default {
  name: 'app',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      canvas: '',
      windowSize: '500',
      bounds: null,
      goodTop: null,
      goodLeft: null,
      boundsSize: 0,
      canvasSize: 0,
      oldGridSize: 0
    }
  }, //
  mounted () {
    this.windowSize = window.innerWidth - 50
    // get context
    this.canvas = new fabric.Canvas('c')

    this.canvas.setWidth( this.windowSize )
    this.canvas.setHeight( this.windowSize )
    this.canvasSize = this.windowSize

    // create room
    let room = new window.fabric.Rect({
      top: 0,
      left: 0,
      width: 150,
      height: 50,
      fill: 'transparent',
      stroke: 'black',
      strokeWidth: 0.5,
      Zindex: -1,
      class: 'room',
      id: ''
    })
    room.setControlsVisibility({
      mtr: false
    })

    // create a rectangle with angle=45
    var rect = new fabric.Rect({
      left: 0,
      top: 0,
      fill: 'purple',
      width:  100,
      height: 100,
      angle: 0
    });

    var text = new fabric.Text('C1', { left: 0, top: 0 })
    var text2 = new fabric.Text('c2', { left: 0, top: 10 })

    let cGroup = new window.fabric.Group([text, text2], {
      top: 200,
      left: 200
    })


    // create bounds rect
    this.bounds = new window.fabric.Rect({
      strokeWidth: 5,
      stroke: 'purple',
      top: -10,
      left: -10,
      width: this.canvas.getWidth() + 20, // this.canvas.getWidth() + 10
      height: this.canvas.getWidth() + 20, // this.canvas.getHeight() + 10
      fill: 'transparent',
      hasBorders: false,
      hasControls: false,
      lockMovementX: true,
      lockMovementY: true,
      selectable: false,
      evented: false,
      id: 'bounds',
      Zindex: -2,
      class: 'bounds'
    })

    this.canvas.add(room, rect, cGroup, this.bounds)

    cGroup.scaleToHeight(200)

    // we need to assign grid size to a variable because it cannot be added directly
    var gridSize = ( this.windowSize + 50 ) / 10 - 5
    this.oldGridSize = gridSize

    let objects = this.canvas.getObjects()
    for (let i = 0; i < objects.length; i++) {
      if (objects[i].get('type') === 'rect' & objects[i].class !== 'bounds') {
        this.canvas.getObjects()[i].scaleToHeight(gridSize)
      }
    }

    // resize listener
    window.addEventListener("resize", this.resize)

    // setup grid / grid snap!
    this.canvas.on('object:moving', function (options) {
      options.target.set({
        left: Math.round(options.target.left / gridSize) * gridSize,
        top: Math.round(options.target.top / gridSize) * gridSize
      })
    })

    // add object moving listener
    let that = this
    this.canvas.on('object:moving', function (obj) {
      obj = obj.target
      obj.setCoords()
      if (!obj.isContainedWithinObject(that.bounds)) {
        obj.setTop(that.goodTop)
        obj.setLeft(that.goodLeft)
        obj.setCoords()
      } else {
        that.goodTop = obj.top
        that.goodLeft = obj.left
      }
    })

    // deleteme
    this.boundsSize = window.innerWidth

    // deleteme
    for ( let i = 0; i < this.canvas.getObjects().length; i++ ) {
      if(this.canvas.getObjects()[i].class === 'room')
        console.log(this.canvas.getObjects()[i])
    }

  },
  methods: {
    resize () {
      // keep track of window size
      let oldWindowSize = this.windowSize - 50

      // set window size variable
      this.windowSize = window.innerWidth - 50

      // grid size
      let gridSize = ( this.windowSize + 50 ) / 10 - 5

      // adjust canvas height and width
      this.canvas.setWidth(this.windowSize)
      this.canvas.setHeight(this.windowSize)

      this.canvasSize = this.windowSize

      // remove object moving listener so we can update the values
      this.canvas.off()

      //replace grid with new params
      this.canvas.on('object:moving', function (options) {
        options.target.set({
          left: Math.round(options.target.left / gridSize) * gridSize,
          top: Math.round(options.target.top / gridSize) * gridSize
        })
      })

      // this was removed when window was resized so we need to read it
      let that = this
      this.canvas.on('object:moving', function (obj) {
        obj = obj.target
        obj.setCoords()
        if (!obj.isContainedWithinObject(that.bounds)) {
          obj.setTop(that.goodTop)
          obj.setLeft(that.goodLeft)
          obj.setCoords()
        } else {
          that.goodTop = obj.top
          that.goodLeft = obj.left
        }
      })

      // scale bounds
      let objects = this.canvas.getObjects()
      for (let i = 0; i < objects.length; i++) {
        if( objects[i].class === 'bounds' ) {

          this.canvas.getObjects()[i].scaleToHeight(this.canvas.getWidth() + 10)

          this.boundsSize = this.canvas.getWidth() + 10

          this.canvas.getObjects()[i].setCoords()
        }
      }

      for (let i = 0; i < objects.length; i++) {
        if( objects[i].class === 'room' ) {

          // set height w/o scaling approach
//          let col = Math.round(this.canvas.getObjects()[i].left/this.oldGridSize)
//          let row = Math.round(this.canvas.getObjects()[i].top/this.oldGridSize)

          // get old height ( it doesn't change when scaled ) and find the ratio of oldHeight/oldWindowSize
//          let newRoomHeight = 100
//          let newRoomWidth = 100

          //this.canvas.getObjects()[i].setHeight(newRoomHeight)
          //this.canvas.getObjects()[i].setWidth(newRoomWidth)
//
//          let top =  Math.floor(gridSize*row)
//          let left = Math.floor(gridSize*col)

//          let scaleTo = this.canvas.getObjects()[i].getHeight()//oldWindowSize*this.windowSize

//          console.log("")
//          console.log(this.canvas.getObjects()[i])
//          console.log("this.canvas.height: " + this.canvas.height)
//          console.log("this.windowSize: " + this.windowSize)
//          console.log("height of object: " + this.canvas.getObjects()[i].height)
//          console.log("Height of Object / old window size * current window size")
//          console.log(this.canvas.getObjects()[i].height + " / " + oldWindowSize + " * " +  this.windowSize)
//          console.log("ratio of height to old window size * this.windowSize")
//          console.log(this.canvas.getObjects()[i].height / oldWindowSize + " * " +  this.windowSize)
//          console.log("Example of calculated height to scale to: " + scaleTo)
//          console.log("")

          // does not work!!
          // let newScaleX = this.canvas.getObjects()[i].scaleX * oldWindowSize / this.windowSize
          // this.canvas.getObjects()[i].scaleX = newScaleX
          // this.canvas.getObjects()[i].scaleToHeight( this.canvas.getObjects()[i].getHeight() )

          // does not work
          //this.canvas.getObjects()[i].scaleTowidth(this.canvas.getObjects()[i].width / oldWindowSize * (this.canvas.width - 50))

          // assign calculated row and column
          //this.canvas.getObjects()[i].top = 300
          //this.canvas.getObjects()[i].left = 300

          this.canvas.getObjects()[i].setHeight(this.canvas.getObjects()[i].height  / oldWindowSize * (this.canvas.height - 50))
          this.canvas.getObjects()[i].setWidth(this.canvas.getObjects()[i].width / oldWindowSize * (this.canvas.height - 50))

          //this.applyGrid(this.canvas.getObjects()[i], gridSize)
          let col = Math.round(this.canvas.getObjects()[i].left/this.oldGridSize)
          let row = Math.round(this.canvas.getObjects()[i].top/this.oldGridSize)

          let top =  Math.floor(gridSize*row)
          let left = Math.floor(gridSize*col)

          // assign calculated row and column
          this.canvas.getObjects()[i].top = top
          this.canvas.getObjects()[i].left = left

          this.canvas.getObjects()[i].setCoords()

          this.canvas.renderAll()
          this.canvas.calcOffset()
        }
      }

      // this works for scaling fonts
      for ( let i = 0; i < objects.length; i++ ) {
        if ( objects[i].class !== 'bounds' & objects[i].class !== 'room') {
          this.canvas.getObjects()[i].scaleToHeight(gridSize-1)

          let col = Math.round(this.canvas.getObjects()[i].left/this.oldGridSize)
          let row = Math.round(this.canvas.getObjects()[i].top/this.oldGridSize)

          let top =  Math.floor(gridSize*row)
          let left = Math.floor(gridSize*col)

          // assign calculated row and column
          this.canvas.getObjects()[i].top = top
          this.canvas.getObjects()[i].left = left
          this.canvas.getObjects()[i].setCoords()
        }
      }

      // reset coordinates
      this.canvas.renderAll()

      // this should fix the 'click area' after objects have been changed, also refresh canvas
      this.canvas.calcOffset()

      // what is the new 'old grid size'
      this.oldGridSize = ( this.windowSize + 50 ) / 10 - 5

    }
  }
}
</script>

<style>

canvas {
  border-style: solid;
}

</style>
