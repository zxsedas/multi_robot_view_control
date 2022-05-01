<template>
        <div id="bot1-wrap">
            <img id="bot1-view" v-bind:src="imgSrc">
        </div>   
</template>


<script>
    import { Base64 } from 'js-base64'
   export default {
    props:['ros','ROSLIB','view_sub_name'],
    data(){
        return {
            imgSrc: null,
            raw: '',
            can: document.createElement("canvas"),
        }
    },
    methods:{
        createImgTopic(){
            this.imgPublisher = new this.ROSLIB.Topic({
                ros: this.ros,
                name: this.view_sub_name,
                messageType: 'std_msgs/msg/String'
            })
        },
        subImgTopic(){         
            this.imgPublisher.subscribe((message)=>{
                this.imgSrc = "data:image/jpg;base64," + message.data
            })
        },
        rgb8ToImg(message){
            this.can.width = message.width;
            this.can.height = message.height;
            const ctx = this.can.getContext("2d");
            const imgData = ctx.createImageData(message.width, message.height);
            const data = imgData.data;
            // const inData = Buffer.from(message.data, 'base64').toString('utf8');
            const inData = Base64.atob(message.data)



            let j = 0;
            let i = 4; // j data in , i data out
            while (j < inData.length) {
                const w1 = inData.charCodeAt(j++); // read 3 16 bit words represent 1 pixel
                const w2 = inData.charCodeAt(j++);
                const w3 = inData.charCodeAt(j++);
                console.log(w1)
                data[i++] = w1; // red
                data[i++] = w2; // green
                data[i++] = w3; // blue
                data[i++] = 255; // alpha
            }
            
            ctx.putImageData(imgData, 0, 0);
            this.imgSrc= this.can.toDataURL();
            // console.log(this.imgSrc)
        },
    },
    mounted() {
            this.createImgTopic()
            this.subImgTopic()
    },
    unmounted(){
        this.imgPublisher.unsubscribe()
    }
   }
</script>

<style scoped>
    /* @media all and (max-width: 500px){
        #bot1-wrap{
            width: 300px;
        }
    }
    @media all and (min-width: 500px){
        #bot1-wrap{
            width: 50%;
        }        
    } */
    #bot1-wrap{
        padding: 10px;  
        display: inline-block;
        vertical-align: middle;
        width: 500px;
    }
    #bot1-view{
        box-shadow: 1px 1px 15px 5px black;
        border-radius: 3px;
        width: 100%;
        height: 100%;
    }
</style>