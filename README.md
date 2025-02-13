<!DOCTYPE html>
<html> 
  <head>
    <title>Hexomino: HTML</title> 
  </head>
  <body>
	<!--This draws the canvas on the webpage -->
    <canvas id="mycanvas"></canvas> 
  </body>
 
  <!-- Include the processing.js library -->
  <!-- See https://khanacademy.zendesk.com/hc/en-us/articles/202260404-What-parts-of-ProcessingJS-does-Khan-Academy-support- for differences -->
  <script src="https://cdn.jsdelivr.net/processing.js/1.4.8/processing.min.js"></script> 
  <script>
  var programCode = function(processingInstance) {
    with (processingInstance) {
      size(400, 400); 
      frameRate(60);
var scene = 0;
var back = color(46,0,1);

var clears = [
  0,0,0,0,0,0,0,0,0,0,0,0,
  0,0,0,0,0,0,0,0,0,0,0,0
];

textFont(createFont("monospace"));
var mouseXReal = mouseXReal+0;
var mouseYReal = mouseYReal+0;
var selectVoid = false;
var iCK = function(col,m){
    var num = null;
    switch(m){
        case 1:
            num = 255-abs((col-(col%pow(2,16)))/pow(2,16));
            break;
        case 2:
            var n2 = abs((col-(col%pow(2,16))));
            var n1 = abs((col-(col%pow(2,8))));
            num = 255-((n1-n2)/pow(2,8));
            break;
        case 3:
            var n1 = abs((col-(col%pow(2,8))));
            num = (255-(abs(col)-n1));
            if(num === 255){
                num = 0;
            }else{
                num += 1;
            }
    }
    return num;
};

var grid = [
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0,  
    0,0,0,0,0,0,0,0,0,0,0,0  
];
var datOtherFrame = false;
var selected = null;
var mult=1;
var blocks = [
    //hex 1{
    [0,0,1,0,2,0,3,0,4,0,5,0],[0,0,0,1,0,2,0,3,0,4,0,5],
    [0,0,1,0,2,0,3,0,4,0,5,0],[0,0,0,1,0,2,0,3,0,4,0,5],
    [0,0,1,0,2,0,3,0,4,0,5,0],[0,0,0,1,0,2,0,3,0,4,0,5],
    [0,0,0,1,1,1,2,1,3,1,4,1],[0,0,1,0,2,0,3,0,4,0,4,1],[0,0,0,1,0,2,0,3,0,4,1,0],[1,0,1,1,1,2,1,3,1,4,0,4],
    [1,0,0,1,1,1,2,1,3,1,4,1],[0,0,1,0,2,0,3,0,4,0,3,1],[0,0,0,1,0,2,0,3,0,4,1,1],[1,0,1,1,1,2,1,3,1,4,0,3],
    [2,0,0,1,1,1,2,1,3,1,4,1],[0,0,1,0,2,0,3,0,4,0,2,1],[0,0,0,1,0,2,0,3,0,4,1,2],[1,0,1,1,1,2,1,3,1,4,0,2],
    [1,0,0,0,1,1,2,1,3,1,4,1],[0,0,1,0,2,0,3,0,4,1,3,1],[1,0,0,1,0,2,0,3,0,4,1,1],[1,0,1,1,1,2,1,3,0,4,0,3],
    [1,0,2,0,1,1,2,1,3,1,4,1],[0,0,1,0,2,0,3,0,2,1,3,1],[1,2,0,1,0,2,0,3,0,4,1,1],[1,0,1,1,1,2,1,3,0,2,0,3],
    [1,0,3,0,1,1,2,1,3,1,4,1],[0,0,1,0,2,0,3,0,1,1,3,1],[1,2,0,1,0,2,0,3,0,0,1,0],[1,0,1,1,1,2,1,3,0,3,0,1],
    //}hex2{
    [0,0,3,0,1,1,2,1,3,1,0,1],[0,0,1,0,2,0,3,0,0,1,3,1],[1,3,0,1,0,2,0,3,0,0,1,0],[1,0,1,1,1,2,1,3,0,3,0,0],
    [2,0,1,0,1,1,2,1,3,1,0,1],[0,0,1,0,2,0,3,0,1,1,2,1],[1,2,0,1,0,2,0,3,0,0,1,1],[1,0,1,1,1,2,1,3,0,2,0,1],
    [0,1,0,0,1,2,2,2,3,2,0,2],[0,0,1,0,2,0,3,0,3,2,3,1],[2,0,0,1,0,2,0,3,0,0,1,0],[2,0,2,1,2,2,2,3,0,3,1,3],
    [1,1,1,0,1,2,2,2,3,2,0,2],[0,0,1,0,2,0,3,0,2,2,2,1],[2,1,0,1,0,2,0,3,0,0,1,1],[2,0,2,1,2,2,2,3,0,2,1,2],
    [0,1,0,0,1,1,2,1,3,1,0,2],[0,1,1,1,2,1,3,0,3,2,3,1],[2,0,1,1,1,2,1,3,0,0,1,0],[1,0,1,1,1,2,2,3,0,3,1,3],
    [0,1,0,0,1,1,2,1,3,1,1,2],[0,1,1,1,2,1,2,0,3,2,3,1],[2,0,1,1,1,2,1,3,0,1,1,0],[1,0,1,1,1,2,2,2,0,3,1,3],
    [0,1,0,0,1,1,2,1,3,1,2,2],[0,1,1,1,2,1,1,0,3,2,3,1],[2,0,1,1,1,2,1,3,0,2,1,0],[1,0,1,1,1,2,2,1,0,3,1,3],
    //}hex3{
    [0,1,0,0,1,1,2,1,3,1,3,2],[0,1,1,1,2,1,0,0,3,2,3,1],[2,0,1,1,1,2,1,3,0,3,1,0],[1,0,1,1,1,2,2,0,0,3,1,3],
    [0,1,1,0,1,1,2,1,3,1,2,2],[0,1,1,1,2,1,1,0,2,2,3,1],[2,1,1,1,1,2,1,3,0,2,1,0],[1,0,1,1,1,2,2,1,0,2,1,3],
    [0,1,1,0,1,1,2,1,3,1,1,2],[0,1,1,1,2,1,2,0,2,2,3,1],[2,1,1,1,1,2,1,3,0,1,1,0],[1,0,1,1,1,2,2,2,0,2,1,3],
    [0,1,1,0,1,1,2,2,3,2,1,2],[0,0,1,0,2,1,2,0,2,2,3,1],[2,1,1,1,0,2,0,3,0,1,1,0],[2,0,2,1,1,2,2,2,0,2,1,3],
    [0,0,3,0,1,0,2,1,3,1,1,1],[0,0,1,0,2,1,2,0,0,1,3,1],[1,3,1,1,0,2,0,3,0,1,1,0],[1,0,1,1,0,2,1,2,0,0,0,3],
    [1,0,0,0,2,0,2,1,3,1,4,1],[0,1,1,1,2,0,2,1,4,0,3,0],[1,0,1,2,0,2,0,3,0,4,1,1],[0,0,0,1,1,2,0,2,1,4,1,3],
    [1,0,0,0,2,0,2,1,3,1,1,1],[0,1,1,1,2,0,2,1,1,0,3,0],[1,0,1,2,0,2,0,3,0,1,1,1],[0,0,0,1,1,2,0,2,1,1,1,3],
    //}hex4{
    [1,0,0,0,2,0,2,1,0,1,1,1],[0,1,1,1,2,0,2,1,1,0,0,0],
    [1,0,0,0,2,0,2,1,0,1,1,1],[0,1,1,1,2,0,2,1,1,0,0,0],
    [1,0,0,0,2,0,2,1,0,1,1,1],[0,1,1,1,2,0,2,1,1,0,0,0],
    [0,0,1,0,1,1,2,1,3,1,1,2],[0,1,1,1,2,1,2,0,2,2,3,2],[2,1,1,1,1,2,1,3,0,1,2,0],[1,0,1,1,1,2,2,2,0,2,0,3],
    [1,0,0,0,0,1,1,1,2,1,0,2],[0,1,1,1,2,1,2,0,2,2,1,2],[2,1,1,0,1,1,1,2,0,0,2,0],[1,0,1,1,1,2,2,2,0,2,0,1],
    [0,0,1,0,1,1,2,1,3,1,2,2],[0,1,1,1,2,1,1,0,2,2,3,2],[2,1,1,1,1,2,1,3,0,2,2,0],[1,0,1,1,1,2,2,1,0,2,0,3],
    [0,0,1,0,1,1,2,2,3,2,1,2],[0,0,1,0,2,1,2,0,2,2,3,2],[2,1,1,1,0,2,0,3,0,1,2,0],[2,0,2,1,1,2,2,2,0,2,0,3],
    [0,0,0,1,1,1,2,2,3,2,1,2],[0,0,1,0,2,1,2,0,3,1,3,2],[1,0,1,1,0,2,0,3,0,1,2,0],[2,0,2,1,1,2,2,2,1,3,0,3],
    [1,0,0,0,0,1,1,2,2,2,0,2],[0,0,1,0,2,1,2,0,2,2,1,2],[2,1,1,0,0,1,0,2,0,0,2,0],[2,0,2,1,1,2,2,2,0,2,0,1],
    //}hex5{
    [1,0,2,0,1,1,1,2,0,0,2,2],[0,0,1,0,0,2,1,1,2,2,1,2],[2,1,1,1,0,1,0,2,2,2,2,0],[2,0,2,1,1,1,0,0,0,2,0,1],
    [1,0,2,0,1,1,1,2,0,1,2,2],[0,0,1,0,0,2,1,1,2,1,1,2],[2,1,1,1,0,1,0,2,2,2,1,0],[2,0,2,1,1,1,0,0,1,2,0,1],
    [0,0,3,2,1,0,2,1,3,1,1,1],[0,0,1,1,2,2,2,1,0,1,3,2],[1,3,2,1,1,2,0,3,1,1,2,0],[1,0,1,1,0,2,1,2,2,0,0,3],
    [1,1,0,0,0,1,1,2,2,2,0,2],[0,0,1,0,2,1,2,0,2,2,1,1],[1,1,1,0,0,1,0,2,0,0,2,0],[2,0,2,1,1,2,2,2,0,2,1,1],
    [1,1,1,0,2,1,1,2,2,2,0,1],[0,1,1,0,2,1,2,0,1,2,1,1],[1,1,1,0,0,1,1,2,0,0,2,1],[1,0,2,1,0,2,1,2,0,1,1,1],
    [1,1,1,0,2,1,1,2,2,2,0,0],[0,1,1,0,2,1,2,0,0,2,1,1],[1,1,1,0,0,1,1,2,0,0,2,2],[2,0,2,1,0,2,1,2,0,1,1,1],
    [0,0,3,2,1,0,2,1,2,2,1,1],[2,0,1,1,0,2,2,1,3,0,1,2],[0,2,2,1,1,2,0,3,1,1,2,0],[2,3,1,1,0,0,1,2,2,2,0,1],
    //}
    //tetro{
    [0,0,1,0,2,0,3,0],[0,0,0,1,0,2,0,3],
    [0,0,1,0,0,1,1,1],
    [0,0,0,1,0,2,1,2],[0,0,1,1,1,0,1,2],[0,0,0,1,1,0,2,0],[1,1,0,1,2,1,2,0],
    [1,0,1,1,0,2,1,2],[0,0,0,1,1,0,0,2],[0,0,0,1,1,1,2,1],[1,0,0,0,2,1,2,0],
    [0,0,0,1,1,1,1,2],[0,0,1,1,0,1,1,2],[1,1,0,1,1,0,2,0],[1,1,0,1,1,0,2,0],
    [1,0,1,1,0,2,0,1],[1,1,0,1,1,0,0,2],[0,0,1,0,1,1,2,1],[1,0,0,0,2,1,1,1],
    [0,0,1,1,0,2,0,1],[1,1,0,1,1,0,1,2],[0,1,1,0,1,1,2,1],[1,0,0,0,2,0,1,1],
    [0,0,1,0,2,0,3,0],[0,0,0,1,0,2,0,3],
    [0,0,1,0,0,1,1,1],
    [0,0,0,1,0,2,1,2],[0,0,1,1,1,0,1,2],[0,0,0,1,1,0,2,0],[1,1,0,1,2,1,2,0],
    [1,0,1,1,0,2,1,2],[0,0,0,1,1,0,0,2],[0,0,0,1,1,1,2,1],[1,0,0,0,2,1,2,0],
    [0,0,0,1,1,1,1,2],[0,0,1,1,0,1,1,2],[1,1,0,1,1,0,2,0],[1,1,0,1,1,0,2,0],
    [1,0,1,1,0,2,0,1],[1,1,0,1,1,0,0,2],[0,0,1,0,1,1,2,1],[1,0,0,0,2,1,1,1],
    [0,0,1,1,0,2,0,1],[1,1,0,1,1,0,1,2],[0,1,1,0,1,1,2,1],[1,0,0,0,2,0,1,1],
    [0,0],[0,0],[0,0],[0,0],
    
    //}
    
    //alt1{
    [0,0,0,1,1,0,2,0,3,0,4,0],[0,1,1,1,2,1,3,1,4,0,4,1],[0,0,1,1,1,2,1,3,1,4,1,0],[0,0,0,1,0,2,0,3,1,4,0,4],
    [3,0,0,1,1,1,2,1,3,1,4,1],[0,0,1,0,2,0,3,0,4,0,1,1],[0,0,0,1,0,2,0,3,0,4,1,3],[1,0,1,1,1,2,1,3,1,4,0,1],
    [3,0,0,1,1,1,2,1,3,1,4,0],[0,1,1,0,2,0,3,0,4,0,1,1],[0,0,0,1,0,2,0,3,1,4,1,3],[0,0,1,1,1,2,1,3,1,4,0,1],
    [3,0,2,0,1,1,2,1,3,1,0,1],[0,0,1,0,2,0,3,0,1,1,0,1],[1,2,0,1,0,2,0,3,0,0,1,3],[1,0,1,1,1,2,1,3,0,0,0,1],
    [1,0,3,0,0,1,1,1,2,1,3,1],[0,0,1,0,2,0,3,0,0,1,2,1],[1,3,0,1,0,2,0,3,0,0,1,1],[1,0,1,1,1,2,1,3,0,2,0,0],
    
    
    //}alt2{
    [3,1,3,0,1,2,2,2,3,2,0,2],[0,0,1,0,2,0,3,0,0,2,0,1],[2,3,0,1,0,2,0,3,0,0,1,3],[2,0,2,1,2,2,2,3,0,0,1,0],
    [2,1,2,0,1,2,2,2,3,2,0,2],[0,0,1,0,2,0,3,0,1,2,1,1],[2,2,0,1,0,2,0,3,0,0,1,2],[2,0,2,1,2,2,2,3,0,1,1,1],
    [0,1,1,0,1,1,2,1,3,1,0,2],[0,1,1,1,2,1,3,0,2,2,3,1],[0,0,1,1,1,2,1,3,2,1,1,0],[1,0,1,1,1,2,2,3,0,2,1,3],
    [0,1,2,0,1,1,2,1,3,1,0,2],[0,1,1,1,2,1,1,0,3,2,3,1],[2,3,1,1,1,2,1,3,0,1,1,0],[1,0,1,1,1,2,2,2,0,0,1,3],
    //}
    //alt3{
        [0,1,0,2,1,1,2,1,3,1,3,0],[0,1,1,1,2,1,0,2,3,0,3,1],[0,0,1,1,1,2,1,3,2,3,1,0],[1,0,1,1,1,2,0,0,2,3,1,3],
        [0,1,2,0,1,1,2,1,3,1,1,2],[0,1,1,1,2,1,2,0,1,2,3,1],[2,2,1,1,1,2,1,3,0,1,1,0],[1,0,1,1,1,2,2,2,0,1,1,3],
        [0,1,1,0,1,1,2,0,3,0,1,2],[0,2,1,2,2,1,2,0,2,2,3,1],[2,1,1,1,2,2,2,3,0,1,1,0],[0,0,0,1,1,2,2,2,0,2,1,3],
        [0,0,2,0,3,0,1,1,2,1,0,1],[1,0,2,0,1,1,3,0,0,1,3,1],[1,3,1,2,0,1,0,2,0,0,1,0],[1,1,1,2,0,1,1,3,0,0,0,3],
        [1,0,0,0,2,0,2,1,3,1,4,1],[0,1,1,1,2,0,2,1,4,0,3,0],[1,0,1,2,0,2,0,3,0,4,1,1],[0,0,0,1,1,2,0,2,1,4,1,3],
    [1,0,0,0,2,0,2,1,3,1,1,1],[0,1,1,1,2,0,2,1,1,0,3,0],[1,0,1,2,0,2,0,3,0,1,1,1],[0,0,0,1,1,2,0,2,1,1,1,3],
    //}alt4{
        [0,2,1,0,1,1,2,1,3,1,1,2],[0,1,1,1,2,1,2,0,2,2,3,0],[2,1,1,1,1,2,1,3,0,1,0,0],[1,0,1,1,1,2,2,2,0,2,2,3],
        [1,2,0,0,0,1,1,1,2,1,0,2],[0,1,1,1,2,1,2,0,2,2,1,0],[0,1,1,0,1,1,1,2,0,0,2,0],[1,0,1,1,1,2,2,2,0,2,2,1],
        [2,0,3,0,0,1,1,1,2,1,1,2],[1,1,2,1,3,1,2,0,1,2,0,2],[2,3,1,0,1,1,1,2,0,1,2,2],[1,1,1,2,1,3,2,2,0,0,0,1],
        [0,2,1,0,1,1,2,0,3,0,1,2],[0,2,1,2,2,1,2,0,2,2,3,0],[2,1,1,1,2,2,2,3,0,1,0,0],[0,0,0,1,1,2,2,2,0,2,2,3],
        [0,2,0,1,1,1,2,0,3,0,1,0],[0,2,1,2,2,1,2,2,3,1,3,0],[1,0,1,1,2,2,2,3,2,1,0,0],[0,0,0,1,1,2,0,2,1,3,2,3],
        [1,0,0,0,0,1,1,2,2,0,0,2],[0,2,1,0,2,1,2,0,2,2,1,2],[2,1,1,0,0,1,2,2,0,0,2,0],[0,0,2,1,1,2,2,2,0,2,0,1],
    //}alt5{
        [1,0,2,0,1,1,1,2,0,2,2,2],[0,0,1,0,0,2,1,1,2,0,1,2],[2,1,1,1,0,1,0,2,2,2,0,0],[2,0,2,1,1,1,0,0,2,2,0,1],
        [0,2,3,0,1,2,2,1,3,1,1,1],[0,2,1,1,2,0,2,1,0,1,3,0],[1,3,0,1,1,2,2,3,1,1,0,0],[1,0,1,1,2,2,1,2,0,0,2,3],
        [0,1,2,0,1,1,0,2,1,2,1,0],[0,1,1,1,2,2,2,1,0,0,1,2],[2,1,2,0,1,1,1,2,1,0,0,2],[2,2,2,1,0,1,1,1,0,0,1,0],
        [0,0,3,2,1,0,2,1,2,2,1,1],[2,0,1,1,0,2,2,1,3,0,1,2],[0,2,2,1,1,2,0,3,1,1,2,0],[2,3,1,1,0,0,1,2,2,2,0,1],
    //}
    
    
];
var kpN = false;
var kp0 = false;
var kp = false;
var mp = false;
var mr = false;
var mp2 = false;
var score = 0;
var scoreHere = 0;
var keys = {};
var xOff = null;
var yOff = null;
var datFrame = false;
var possible = [260,round(random(0,blocks.length-1)),round(random(0,blocks.length-1))];
var npossible = [round(random(0,blocks.length-1)),round(random(0,blocks.length-1)),round(random(0,blocks.length-1))];
var voids = [false,true,false];
var specKeys = {};

draw = function() {
    mouseXReal = mouseX + 0;
    mouseYReal = mouseY + 0;
    kpN = false;
    kp0 = false;
    mouseReleased = function(){mr = true; mp2 = false;};
    mousePressed = function(){mp = true;mp2 = true;};
    keyPressed = function(){keys[key.code] = true; kp = true;kp0 = true;kpN = true;  if(keyCode === 37 || keyCode === 38 || keyCode === 39 || keyCode === 40 || keyCode === SHIFT || keyCode === CONTROL){specKeys[keyCode]=true;kpN = true;}
};
    keyReleased = function(){keys[key.code] = false;
    if(keyCode === 37 || keyCode === 38 || keyCode === 39 || keyCode === 40 || keyCode === SHIFT || keyCode === CONTROL){specKeys[keyCode]=false;}
    };

    
    switch(scene){
        case 0:
            //main menu
            noStroke();
            background(back);
            fill(back * color(10,10,10));
            rect(0,0,400,100);
            fill((back * color(10,10,10)),150);
            rect(0,100,400,30);
            
            fill(back * color(10,10,10));
            stroke(back * color(6,6,6));
            strokeWeight(10);
            rect(30,140,270,50);
            if(mp&&mouseXReal>=30&&mouseXReal<=300&&mouseYReal>=140&&mouseYReal<= 190){
                scene = 1;
            }
            
            textSize(50);
            
            fill(0,0,0,255);
            text("Hexomino",13,58);
            fill(0,0,0,200);
            text("Hexomino",11,56);
            fill(0,0,0,150);
            text("Hexomino",9,54);
            fill(0,0,0,100);
            text("Hexomino",7,52);

            fill(255, 255, 255);
            text("Hexomino",15,60);
            
            
            textSize(45);
            fill(0,0,0,255);
            text("Play",100,177);
            fill(255,255,255);
            text("Play",105,177);
            
            //fill(0, 0, 0);
            noStroke();
            rect(347.5,147.5,25,150);
            strokeWeight(5);
            fill(17, 0, 209);
            stroke(5, 13, 69);
            rect(350,150,20,20);
            rect(350,175,20,20);
            rect(350,200,20,20);
            rect(350,225,20,20);
            rect(350,250,20,20);
            rect(350,275,20,20);
            
            break;
        case 1:
            scoreHere=0;
            //game (:O)
            background(back);
            fill(183, 0, 255);
            textSize(30);
            pushMatrix();
            rotate(PI/2);
            text("(x"+mult+")",250,-350);
            fill(255);
            textSize(50);
            text(score,100,-340);
            popMatrix();
            textSize(30);
            text("NEXT",5,24);
            strokeWeight(2.5);
            strokeCap(SQUARE);
            stroke(back * color(10,10,10),255);
            for(var a = 0; a<=12;a++){
                line(a * 20 + 80, 0, a * 20 + 80, 240);
                line(80, a* 20, 320, a*20);
            }
            
            fill(back * color(2,20,4),150);
            stroke(back * color(2,20,4),255);
            for(var a = 0; a < blocks[npossible[0]].length; a+=2){
                    rect(blocks[npossible[0]][a]*10 + 10, blocks[npossible[0]][a+1]*10+40, 7.5,7.5);
            }
            for(var a = 0; a < blocks[npossible[1]].length; a+=2){
                    rect(blocks[npossible[1]][a]*10 + 10, blocks[npossible[1]][a+1]*10+110, 7.5,7.5);
            }
            for(var a = 0; a < blocks[npossible[2]].length; a+=2){
                    rect(blocks[npossible[2]][a]*10 + 10, blocks[npossible[2]][a+1]*10+180, 7.5,7.5);
            }
            
            
            
            
            noStroke();
            strokeWeight(5);
            fill(back * color(10,14,26),150);
            stroke(back * color(10,14,26),255);

            for(var a = 0; a<=11;a++){
                for(var b = 0; b<=11;b++){
                    if(grid[a*12+b] === 1){
                        rect(b*20+81.25,a*20+3,15,15);
                    }
                }
            }
            
            for(var a = 0; a<=10;a++){
                if(clears[a] !== 0){
                    var amnt = 255;
                    if(clears[a]<=5){
                        amnt=clears[a]*50;
                    }
                    if(clears[a]>=10){
                        amnt=255-((clears[a]-10)*40.3);
                    }
                    fill(255,255,255,amnt);
                    noStroke();
                    rect(a*20+80,0,20,240);
                    clears[a]+=1.5;
                    if(clears[a]>=25){clears[a]=0;}
                }
                
            }
            
            for(var a = 12; a<=23;a++){
                if(clears[a] !== 0){
                    var amnt = 255;
                    if(clears[a]<=5){
                        amnt=clears[a]*50;
                    }
                    if(clears[a]>=10){
                        amnt=255-((clears[a]-10)*40.3);
                    }
                    fill(255,255,255,amnt);
                    noStroke();
                    rect(80,a*20-240,240,20);
                    clears[a]+=1.5;
                    if(clears[a]>=25){clears[a]=0;}
                }
                
            }
            fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),150);
            stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),255);
            if(voids[0]){
                fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 123, 1),150);
                stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 123, 1),255);}else{
                fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),150);
                stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),255);
            }
            if(possible[0]!==null){
                for(var a = 0; a < blocks[possible[0]].length; a+=2){
                    rect(blocks[possible[0]][a]*20 + 20, blocks[possible[0]][a+1]*20+260, 15, 15);
                }
            }
            datFrame=false;
            if(mp&&selected===null&& mouseXReal>20&&mouseXReal<140&&mouseYReal>=260&&mouseYReal<=380){
                selected = possible[0];
                selectVoid = voids[0];
                possible[0] = null;
                xOff = mouseXReal - 20;
                yOff = mouseYReal - 260;
                datFrame = true;
            }
            if(voids[1]){
                fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 123, 1),150);
                stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 123, 1),255);}else{
                fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),150);
                stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),255);
            }
            if(possible[1]!==null){
                for(var a = 0; a < blocks[possible[1]].length; a+=2){
                    rect(blocks[possible[1]][a]*20 + 140, blocks[possible[1]][a+1]*20+260, 15, 15);
                }
            }
            if(mp&&!datFrame&&selected===null&& mouseXReal>140&&mouseXReal<260&&mouseYReal>=260&&mouseYReal<=380){
                selected = possible[1];
                selectVoid = voids[1];
                possible[1] = null;
                
                xOff = mouseXReal - 130;
                yOff = mouseYReal - 260;
                datFrame = true;
            }
            if(voids[2]){
                fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 123, 1),150);
                stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 123, 1),255);}else{
                fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),150);
                stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),255);
            }
            if(possible[2]!==null){
                for(var a = 0; a < blocks[possible[2]].length; a+=2){
                    rect(blocks[possible[2]][a]*20 + 260, blocks[possible[2]][a+1]*20+260, 15, 15);
                }
            }
            if(mp&&!datFrame&&selected===null&& mouseXReal>260&&mouseXReal<380&&mouseYReal>=260&&mouseYReal<=380){
                selected = possible[2];
                selectVoid = voids[2];
                possible[2] = null;
                xOff = mouseXReal - 260;
                yOff = mouseYReal - 260;
                datFrame = true;
            }
                fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),150);
                stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),255);
            var yes = true;
            if(selected!==null){
                for(var a = 0; a < blocks[selected].length; a+=2){
                    if(round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*20>=0&&round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*20<=220 && round((blocks[selected][a]*20 + 0 + mouseXReal - xOff)/20)*20 >= 80 &&round((blocks[selected][a]*20 + 0 + mouseXReal - xOff)/20)*20 <= 300){
                        if(grid[round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*12+(round((blocks[selected][a]*20 + mouseXReal - xOff)/20)-4)]===1&&!selectVoid){
                            yes = false;
                        }

                    }else{yes=false;}
                }
                if(yes&&mp||yes&&mr){
                    for(var a = 0; a < blocks[selected].length; a+=2){
                        grid[round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*12+(round((blocks[selected][a]*20 + mouseXReal - xOff)/20)-4)]=1;
                    }
                    if(!selectVoid){
                        scoreHere += 10*mult;
                    }else{
                        scoreHere -= 30/mult;
                        scoreHere = round(scoreHere);
                    }

                    if(possible[0]===null){
                        voids[0] = false;
                        if(random(0,1)<=0.1&&!voids[1]&&!voids[2]){voids[0]=true;}else{voids[0]=false;}
                        possible[0] = npossible[0];
                        npossible[0] = round(random(0,blocks.length-1));
                    }
                    if(possible[1]===null){
                        voids[1] = false;
                        if(random(0,1)<=0.1&&!voids[0]&&!voids[2]){voids[1]=true;}else{voids[1]=false;}
                        possible[1] = npossible[1];
                        npossible[1] = round(random(0,blocks.length-1));
                    }
                    if(possible[2]===null){
                        voids[2] = false;
                        if(random(0,1)<=0.1&&!voids[1]&&!voids[0]){voids[2]=true;}else{voids[2]=false;}
                        possible[2] = npossible[2];
                        npossible[2] = round(random(0,blocks.length-1));
                    }
                    var can1b = false;
                    var can2b = false;
            for(var a = 0; a<= 11; a++){
                var can1 = true;
                var can2 = true;
                for(var b = 0; b <= 11; b++){
                    if(grid[a*12+b]===0){can1=false;}
                    if(grid[b*12+a]===0){can2=false;}
                }

                if(can1){
                    can1b = true;
                    clears[12+a] = 1;
                    for(var c = 0; c <= 11; c++){
                        grid[a*12+c]=0;
                    }
                    scoreHere += 10;
                    scoreHere *= 2*mult;
                    scoreHere = abs(scoreHere);
                    mult+=1;
                    datOtherFrame = true;
                }
                if(can2){
                    can2b = true;
                    clears[a]=1;
                    for(var c = 0; c <= 11; c++){
                        grid[c*12+a]=0;
                    }
                    scoreHere += 10;
                    scoreHere *= 2*mult;
                    scoreHere = abs(scoreHere);
                    mult+=1;
                    datOtherFrame = true;
                }
                

            }
                    if(!can1b && !can2b){
                        mult-=0.5;
                        mult = constrain(mult,1,25);
                    }
                    if(selectVoid){
                        for(var a = 0; a < blocks[selected].length; a+=2){
                            grid[round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*12+(round((blocks[selected][a]*20 + mouseXReal - xOff)/20)-4)]=0;
                        }
                    }
                    selected = null;
                    selectVoid = false;
                    //scan for solution
                    var canSol = false;
                    for(var a = 0; a<=11; a++){
                        if(!canSol){
                            for(var b = 0; b<=11; b++){
                                if(!canSol){
                                    var yes = true;
                                    for(var c = 0; c < blocks[possible[0]].length; c+=2){
                                        if(round((blocks[possible[0]][c+1]*20+0 )/20)*20+a*20>=0&&round((blocks[possible[0]][c+1]*20)/20)*20+a*20<=220 && round((blocks[possible[0]][c]*20 + 0 )/20)*20+b*20 >= 80 &&round((blocks[possible[0]][c]*20 + 0 )/20)*20+b*20 <= 300){
                                            if(grid[round((blocks[possible[0]][c+1]*20+0 )/20+a)*12+(round((blocks[possible[0]][c]*20 )/20+b)-4)]===1&&!voids[0]){
                                                yes = false;
                                            }
                                        }else{yes=false;}
                                    }
                                    var yes2 = true;
                                    for(var c = 0; c < blocks[possible[1]].length; c+=2){
                                        if(round((blocks[possible[1]][c+1]*20+0)/20)*20+a*20>=0&&round((blocks[possible[1]][c+1]*20)/20)*20+a*20<=220 && round((blocks[possible[1]][c]*20 + 0 )/20)*20+b*20 >= 80 &&round((blocks[possible[1]][c]*20 + 0 )/20)*20+b*20 <= 300){
                                            if(grid[round((blocks[possible[1]][c+1]*20+0)/20+a)*12+(round((blocks[possible[1]][c]*20 )/20+b)-4)]===1&&!voids[1]){
                                                yes2 = false;
                                            }
                                        }else{yes2=false;}
                                    }
                                    var yes3 = true;
                                    for(var c = 0; c < blocks[possible[2]].length; c+=2){
                                        if(round((blocks[possible[2]][c+1]*20+0)/20)*20+a*20>=0&&round((blocks[possible[2]][c+1]*20)/20)*20+a*20<=220 && round((blocks[possible[2]][c]*20 + 0 )/20)*20+b*20 >= 80 &&round((blocks[possible[2]][c]*20 + 0 )/20)*20+b*20 <= 300){
                                            if(grid[round((blocks[possible[2]][c+1]*20+0)/20+a)*12+(round((blocks[possible[2]][c]*20 )/20+b)-4)]===1&&!voids[2]){
                                                yes3 = false;
                                            }
                                        }else{yes3=false;}    
                                    }
                                    
                                    
                                    if(yes||yes2||yes3){canSol=true;
                                        break;
                                    }
                                }
                            }
                        }
                    }
                    if(!canSol){scene = 2;}
                }
                if(!yes&&mp&&!datFrame){
                    if(possible[0]===null){
                        possible[0] = selected;
                    }
                    if(possible[1]===null){
                        possible[1] = selected;
                    }
                    if(possible[2]===null){
                        possible[2] = selected;
                    }
                    selected = null;
                    selectVoid = false;
                }
                if(!yes){
                    fill(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(119, 153, 86),150);
            stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(119, 153, 86),255);

                }
                if(selected !== null){
                    for(var a = 0; a < blocks[selected].length; a+=2){
                        rect(blocks[selected][a]*20 + 0 + mouseXReal - xOff, blocks[selected][a+1]*20+0 + mouseYReal - yOff, 15, 15);
                        if(round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*20>=0&&round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*20<=220 && round((blocks[selected][a]*20 + 0 + mouseXReal - xOff)/20)*20 >= 80 &&round((blocks[selected][a]*20 + 0 + mouseXReal - xOff)/20)*20 <= 300){
                            noStroke();
                            if(grid[round((blocks[selected][a+1]*20+0 + mouseYReal - yOff)/20)*12+(round((blocks[selected][a]*20 + mouseXReal - xOff)/20)-4)]===0&&!selectVoid){
                                rect(round((blocks[selected][a]*20 + mouseXReal - xOff)/20)*20+1,round((blocks[selected][a+1]*20 + mouseYReal - yOff)/20)*20+1,18,18);
                            }
                            stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(91, 120, 61),255);
                            if(!yes){
            stroke(color(iCK(back,3),iCK(back,1),iCK(back,2)) * color(119, 153, 86),255);
                            }
                        }
                    }
                }
                
            }
            
            score += scoreHere;
            break;
        case 2:
            background(back);
            noStroke();
            fill(back * color(10,10,10));
            rect(0,0,400,100);
            fill((back * color(10,10,10)),150);
            rect(0,100,400,30);
            
            
            fill(255);
            textSize(45);
            text("Game over",20,50);
            text(score,280,380);
            textSize(30);
            text("Score:",270,340);
            
            strokeWeight(5);
            strokeCap(SQUARE);
            stroke(back*color(10,10,10),255);
            for(var a = 0; a<=12;a++){
                line(a * 20 + 0, 130, a * 20 + 0, 370);
                line(0, a* 20+130, 240, a*20+130);
            }
            
            fill(back * color(10,14,26),150);
            stroke(back * color(10,14,26),255);

            for(var a = 0; a<=11;a++){
                for(var b = 0; b<=11;b++){
                    if(grid[a*12+b] === 1){
                        rect(b*20,a*20+135,15,15);
                    }
                }
            }
            
            fill(back * color(10,10,10));
            stroke(back * color(6,6,6));
            strokeWeight(10);
            rect(250,140,140,80);
            if(mp&&mouseXReal>=250&&mouseXReal<=390&&mouseYReal>=140&&mouseYReal<= 220){
                grid = [
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0,  
                    0,0,0,0,0,0,0,0,0,0,0,0  
                ];
                selected = null;
                selectVoid = null;
                voids = [false,true,false];
                mult =1;
                score = 0;
                scoreHere = 0;
                possible = [260,round(random(0,blocks.length-1)),round(random(0,blocks.length-1))];
                npossible = [round(random(0,blocks.length-1)),round(random(0,blocks.length-1)),round(random(0,blocks.length-1))];
                
                scene = 0;
                
            }
            textSize(45);
            fill(255);
            text("RESET",251,195);
            
            
    }
    
    mr = false;
    mp = false;
};
    }};
  // Get the canvas that ProcessingJS will use
  var canvas = document.getElementById("mycanvas"); 
  // Pass the function to ProcessingJS constructor
  var processingInstance = new Processing(canvas, programCode); 
  </script>
</html>

