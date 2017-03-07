---
layout: post
title:  "canvas做的烟花"
date:   2017-03-07 15:55:04 +0800
categories: [skill]
excerpt: "canvas实现的烟花效果，没有做参数设置，只有简单实现"
tags:
  - javascript canvas
---

***先看效果***：[DEMO……](/html/fireworks.html)

## 说明

> 我是在学习canvas，看了段时间的文档，想找一个实践的小程序，于是就找到了这个。自己先想了实践，但是到具体实现起来还是各种问题，后来在网上找了别人的代码做参考，才算基本完成。

## 实现的功能

1. 随机在底部产生烟花，发射到随机位置
2. 烟花爆炸，产生随机产生碎片，

> 网上的功能还包括，音乐、背景、月亮、参数配置。不过我觉得这些不重要。

## 最初想法

	我自己想的实现方法，无非就是一个点到另一个点的一个动画。
	其实这也没错，烟花和碎片都是这样。后来我就实现了，一条一条的直线，很粗糙，没发看。

## 几点注意的地方

1. 每次动画用 fillRect 不要用 clearReck，前者会有一个虚影的效果（仔细看虽然背景会有一点点花），后者完全就是一个点了，感觉就是没有了特效。
2. 之前碎片消失，以爆炸点为原点，总是同一y轴一起，并且 |x| 越小越先消失，
导致效果就像开门一样往两边散开。后来我调试无果的情况下，想到了一个更好的方法，那就是随机消失，烟花本身也是这样，总有些小点会亮很久。这样效果好多了。
3. 重力的效果用每次把碎片的目标位置的y加一个固定值。

## 总结

首先考虑面向对象来编写程序，我最先就是想每个功能写一个func的方式，感觉越整越乱了。

还有就是动画步骤是可拆分的，这个动画不算复杂，不过再复杂的动画都是多个动作组成的。

### 最后贴一下源码吧

``` html
<!DOCTYPE HTML>
<head>
    <style>
        body {
            background-color: #1d1f20;
        }
        .canvas-container {
            margin: auto;
            width: 100%;
            height: 100%;
            background-color: #000;
        }
    </style>
</head>
<body>
    <div class="canvas-container">
        <canvas id="canvas"></canvas>
    </div>

<script>
var canvas = document.getElementById('canvas');
if (canvas.getContext){
    var ctx = canvas.getContext('2d');
}
var lastTime;
var bigbooms = [];

window.onload = function(){
    canvas.height = window.innerHeight;
    canvas.width = window.innerWidth;
    lastTime = new Date();
    animate()
}


window.requestAnimFrame = function(){return window.requestAnimationFrame||window.webkitRequestAnimationFrame||window.mozRequestAnimationFrame||window.oRequestAnimationFrame||window.msRequestAnimationFrame||function(a){window.setTimeout(a,1000/60)}}();

var animate = function() {
    ctx.save();
    ctx.globalCompositeOperation = 'destination-out';
    ctx.globalAlpha = 0.1;
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    ctx.restore();
    newTime = new Date();
    if (newTime - lastTime > 500 && bigbooms.length <50) {
        var x = getRandom(canvas.width/8 , canvas.width*7/8);
        var y = getRandom(0 , canvas.height/2);
        var bigboom = new Boom(getRandom(canvas.width/3,canvas.width*2/3) , 2, "rgba("+getRandomInt(0, 255)+","+getRandomInt(0, 255)+","+getRandomInt(0, 255)+", 1)" , {x:x , y:y});
        bigbooms.push(bigboom);
        lastTime = newTime;
    }

    if (bigbooms) {
        bigbooms.forEach(function(that,index){
            if(!that.dead){
                that._move();
                that._drawLight();
            } else {
                if (that.booms) {
                    var fragLive = 0;
                    that.booms.forEach(function(frag){
                        if(!frag.dead) {
                            frag.moveTo();
                            fragLive += 1;
                        }
                    });
                    if (fragLive == 0) {
                        bigbooms.splice(index , 1);
                    }
                }
            }
        });
    }

    requestAnimFrame(animate);
}

var Boom = function(x, r, c, to){
    this.x = x;
    this.y = canvas.height - r;
    this.r = r;
    this.c = c;
    this.to = to;
    this.dead = false;
    this.booms = [];
}
Boom.prototype = {
    _print:function() {
        ctx.save();
        ctx.beginPath();
        ctx.arc(this.x,this.y,this.r,0,2*Math.PI);
        ctx.fillStyle = this.c;
        ctx.fill();
        ctx.restore();
    },
    _move:function() {
        var dx = this.to.x - this.x, dy = this.to.y - this.y;
        this.x = Math.abs(dx) < 10 ? this.x : this.x + dx * 0.01;
        this.y =  Math.abs(dy) < 10 ? this.y : this.y + dy * 0.01;
        if (Math.abs(dx) <= 50 && Math.abs(dy) <= 80) {
            this._boom();
            this.dead = true;
        } else {
            this._print();
        }
    },
    _drawLight:function(){
        ctx.save();
        ctx.fillStyle = "rgba(255,228,150,0.3)";
        ctx.beginPath();
        ctx.arc(this.x , this.y , this.r+3*Math.random()+1 , 0 , 2*Math.PI);
        ctx.fill();
        ctx.restore();
    },
    _boom:function() {
        var fragNum = getRandom(100 , 300);

        var fanwei = fragNum;
        for(var i=0;i<fragNum;i++){
            var color = "rgba("+getRandomInt(0, 255)+","+getRandomInt(0, 255)+","+getRandomInt(0, 255)+", 1)";
            var a = getRandom(-Math.PI, Math.PI);
            var x = getRandom(0, fanwei) * Math.cos(a) + this.x;
            var y = getRandom(0, fanwei) * Math.sin(a) + this.y;
            var radius = getRandom(0 , 2)
            var frag = new Frag(this.x , this.y , radius , color , {x:x, y:y});
            this.booms.push(frag);
        }
    }
}

var Frag = function(x, y, r, c, to) {
    this.x = x;
    this.y = y;
    this.c = c;
    this.r = r;
    this.to = to;
    this.dead = false;
}
Frag.prototype = {
    paint:function(){
        // ctx.beginPath();
        // ctx.arc(this.x , this.y , this.radius , 0 , 2*Math.PI);
        ctx.fillStyle = this.c;
        ctx.fillRect(this.x-this.r , this.y-this.r , this.r*2 , this.r*2)
    },
    moveTo:function(){
        this.to.y = this.to.y + 0.3;
        var dx = this.to.x - this.x , dy = this.to.y - this.y;
        this.x = Math.abs(dx)<0.1 ? this.to.x : (this.x+dx*0.1);
        this.y = Math.abs(dy)<0.3 ? this.to.y : (this.y+dy*0.1);
        if(dx===0 && Math.abs(dy)<=20){
            goDead = getRandom(0, 100);
            if (goDead > 95) {
                this.dead = true;
            }
        }
        this.paint();
    }
};

canvas.onclick = function(){
    var x = event.clientX;
    var y = event.clientY;
    var bigboom = new Boom(getRandom(canvas.width/3,canvas.width*2/3) ,2,"#FFF" , {x:x , y:y});
    bigbooms.push(bigboom)
}

function getRandom(a , b){
    return Math.random()*(b-a)+a;
}
function getRandomInt(a , b){
    return parseInt(Math.random()*(b-a)+a);
}
</script>

</body>
</HTML>
```