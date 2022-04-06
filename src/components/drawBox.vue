<template>
  <div id="app">
    <div style="position: relative;">
      <canvas id="myCanvas" width="560" height="360" style="left: 0; top: 0; z-index: 0;background-color:white"  :style="{ 'background-image': `url(${this.previewImage})`, 'background-repeat':'no-repeat', 'background-size':'cover'}" />
      <canvas id="myCanvas3" width="560" height="360" style="position: absolute; left: 0; top: 0; z-index: 0;" />
      <canvas id="myCanvas2" width="560" height="360" @click="getClicked($event)" @mousedown="[beginDrawing($event)]" @mousemove="[getHoverd($event),keepDrawing($event)]" @mouseup="stopDrawing"
      style="position: absolute; left: 0; top: 0; z-index: 2;" class='cursor-pointer' />
    </div>

    </div>
</template>


<style>

</style>


<script>

var points=[];
// eslint-disable-next-line no-unused-vars
var hoveredBox=[];
// eslint-disable-next-line no-unused-vars
var clickedBox =[];
function drawRect(x,y,xRec,yRec,canvas) {
    canvas.beginPath();
    canvas.rect(x,y,xRec,yRec)
    canvas.fillStyle = 'rgba(0,255,0,0.1)' ;
    canvas.fillRect(x,y,xRec,yRec);
    canvas.stroke();
    canvas.closePath();
}

function drawCircle (x, y, ctx){
    points.push([x,y])
    ctx.fillStyle = 'rgba(0,255,0,0.5)';
    ctx.beginPath();
    ctx.arc(x,y,10,0,2*Math.PI)
    ctx.stroke();
    ctx.fill();

    ctx.closePath();
}

function loadBoxes(boxes,canvas) {
  canvas.strokeStyle = "#00FF00";
  boxes.forEach((box) => {
    let minCor=getMinCor(box)
    drawRect(box[0],box[1],box[2],box[3],canvas)
    drawCircle(box[0],box[1], canvas )
    drawCircle(minCor[0],minCor[1], canvas )
    points=[]
  });
}

function colorBox(box,canvas,color) {
    clearCanvas(canvas);
    canvas.strokeStyle = color
    drawRect(box[0],box[1],box[2],box[3],canvas)
    drawCircle(box[0],box[1],canvas)
    drawCircle(getMinCor(box)[0],getMinCor(box)[1],canvas)
    points=[]
    canvas.strokeStyle = "#00FF00";
}

function clearCanvas(canvas) {
  canvas.clearRect(0, 0, canvas.canvas.clientWidth, canvas.canvas.clientHeight);
}

function getHoverCondition(x,y,box) {
    // check if the box is drawn to the left or upwards 
    let checkerX = isNegative(box[2])
    let checkerY = isNegative(box[3])

    // conditions for the box location(left, up , or both)
    let condition= x>=box[0] && x<=box[0]+box[2] && y>=box[1] && y<=box[1]+box[3]
    if(checkerX){
      condition=x<=box[0] && x>=box[0]+box[2] && y>=box[1] && y<=box[1]+box[3]
    }
    if(checkerY){
      condition=x>=box[0] && x<=box[0]+box[2] && y<=box[1] && y>=box[1]+box[3]
    }
    if(checkerX && checkerY){
      condition=x<=box[0] && this.xDetect>=box[0]+box[2] && y<=box[1] && y>=box[1]+box[3]
    }
  return condition
}

function getMinCor (rect){
  return [rect[0]+rect[2],rect[1]+rect[3]]
}

function isNegative(number) {
  return !Object.is(Math.abs(number), +number);
}

function arraysEqual(a1,a2) {
    return JSON.stringify(a1)==JSON.stringify(a2);
}

export default {
  name: 'drawBox',
  props: ['boxList','drawMode','previewImage',],
  el: '#app',
  mounted() {
    // initialize and draw saved boxes
    // canvas(1) for base drawing
    this.canvas = document.getElementById("myCanvas").getContext('2d');
    // canvas(2) for interactive canvas
    this.canvas2 = document.getElementById("myCanvas2").getContext('2d');
    // canvas(3) for saving selected box 
    this.canvas3 = document.getElementById("myCanvas3").getContext('2d');

    loadBoxes(JSON.parse(JSON.stringify(this.mutableList)),this.canvas)
  },
  methods: {
    getClicked(e){
      if(!this.drawMode){
        this.xDetect = e.offsetX;
        this.yDetect = e.offsetY;

        JSON.parse(JSON.stringify(this.mutableList)).forEach((box)=>{

        // apply condition to get hovered box
        if(getHoverCondition(this.xDetect,this.yDetect,box)){
          clickedBox=box
        }
        // keep the selected choice
        colorBox(clickedBox,this.canvas3,"#0000FF")
        // color the previously hovered box
        colorBox(hoveredBox,this.canvas2,"#00000000")

      })
    }
    },
    getHoverd(e){
      if(!this.drawMode){
        this.xDetect = e.offsetX;
        this.yDetect = e.offsetY;

        // loop through boxes to find the hovered box
        JSON.parse(JSON.stringify(this.mutableList)).forEach((box)=>{

        // apply condition to detect hovered box
        if(getHoverCondition(this.xDetect,this.yDetect,box)){
            hoveredBox=box
          }
        })

        // color the hovered box only if the hovered box is not a slected box
        if(!arraysEqual(hoveredBox,clickedBox)){
          colorBox(hoveredBox,this.canvas2,"#00FF00")
        }

        // if the saved hovered box is not hovered, remove hover
        if(!getHoverCondition(this.xDetect,this.yDetect,hoveredBox)){
          colorBox(hoveredBox,this.canvas2,"#00000000")
        }
      
        points=[]
        this.x = 0;
        this.y = 0;
      }

    },
    beginDrawing(e) {
      if(this.drawMode){
        colorBox(hoveredBox,this.canvas3,"#00000000")

        this.x = e.offsetX;
        this.y = e.offsetY;
        drawCircle(this.x,this.y,this.canvas)
      }
    },
    keepDrawing(e) {
      if (this.drawMode === true) {        
        this.x = e.offsetX;
        this.y = e.offsetY;
        // eslint-disable-next-line no-unused-vars
        let xRec
        // eslint-disable-next-line no-unused-vars
        let YRec
        // grab immediate width and height for rect
        try {
          let xRec=(this.x-points[0][0])
          let YRec=(this.y-points[0][1])
      
          // clear canvas while drawing
          clearCanvas(this.canvas2)

          drawRect(points[0][0],points[0][1],xRec,YRec,this.canvas2)
        } 
        /*eslint no-empty: "error"*/
        catch{
          // empty
        }
      }
    },
    stopDrawing() {
      if (this.drawMode === true) {
        drawCircle(this.x,this.y,this.canvas)

        // draw rect if two points existed
        if(points.length>=2){
          // grab width and height for rect
          let xRec=(points[1][0]-points[0][0])
          let YRec=(points[1][1]-points[0][1])
          
          drawRect(points[0][0],points[0][1],xRec,YRec,this.canvas)

          // emit event on boxList update
          this.mutableList.push([points[0][0],points[0][1],xRec,YRec])
          this.$emit('inputChange', this.mutableList)
          points=[]
        }
        //reset
        this.x = 0;
        this.y = 0;
      }
    }
  },
  data: function () {
    return {
      //starting data
      canvas: null,
      canvas2: null,
      x: 0,
      y: 0,
      xDetect: 0,
      yDetect: 0,
      mutableList: this.boxList
    }
  }
}
</script>