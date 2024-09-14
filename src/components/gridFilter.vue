<template>
    <canvas width="305" height="305" id="canvasFilter"></canvas>
</template>   
  
<script>
    export default{
        data(){
            return{
                ctx: null,
                canvas: null
            }
        },
        mounted(){
            this.canvas = document.getElementById("canvasFilter")
            this.ctx = this.canvas.getContext("2d")
            this.drawBoard(this.ctx)
        },
        methods:{
            drawBoard(context){
                const p = 0
                const bw = 300
                const bh = 300
                for (var x = 0; x <= bw; x += 100) {
                    context.moveTo(0.5 + x + p, p);
                    context.lineTo(0.5 + x + p, bh + p);
                }

                for (var x = 0; x <= bh; x += 100) {
                    context.moveTo(p, 0.5 + x + p);
                    context.lineTo(bw + p, 0.5 + x + p);
                }
                context.strokeStyle = "black";
                context.stroke();
            }   
        }
    }
</script>