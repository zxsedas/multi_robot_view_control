<template>
    <div class="controlArea">
        <h3 class=name>Robot{{ this.id }}</h3>
        <div class="wrap" >
            <div class="key direct" @click="direct">W</div>
            <div class="downArea">
                <div class="key left" @click="left">A</div>
                <div class="key stop" @click="stop">S</div>
                <div class="key right" @click="right">D</div>
            </div>   
        </div>
    
    </div>
</template>

<script>

export default {
    props:['ros','ROSLIB','vel_sub_name','id','active'],
    data(){
        return {
            cmdPublisher: null,
            imgPublisher: null,
            twist: {
                linear: {x: 0.0, y:0.0, z:0.0},
                angular: {x: 0.0, y:0.0, z:0.0},
            }
        }
    },
    watch: {
            active(val){
                if(val){
                    this.keyboardControl()
                }
            }
    },
    methods:{
        createCmdTopic(){
            this.cmdPublisher = new this.ROSLIB.Topic({
                ros: this.ros,
                name: this.vel_sub_name,
                messageType: 'geometry_msgs/Twist'
            })
        },
        direct(){
            this.twist.linear.x += 0.01
            this.cmdPublisher.publish(this.twist) 
        },
        left(){
            this.twist.angular.z += 0.01
            this.cmdPublisher.publish(this.twist) 
        },
        right(){
            this.twist.angular.z -= 0.01
            this.cmdPublisher.publish(this.twist) 
        },
        stop(){
            this.reset()
            this.cmdPublisher.publish(this.twist) 
        },
        reset(){
            this.twist.linear.x = 0.0
            this.twist.linear.y = 0.0
            this.twist.linear.z = 0.0
            this.twist.angular.x = 0.0
            this.twist.angular.y = 0.0
            this.twist.angular.z = 0.0
        },
        keyboardControl(){
            document.onkeydown = () =>{
                let key = window.event.keyCode;      
                if(key == 87){
                    this.direct()
                }
                else if(key == 65){
                    this.left()
                }
                else if(key == 83){  
                    this.stop()
                }
                else if(key == 68){
                    this.right()
                }  
            }                   
                         
        },
        
    },
    mounted(){     
        this.createCmdTopic()            
    },
    unmounted(){
        console.log('unmounted')
        this.stop()
        document.onkeydown = null
    }

}
</script>

<style scoped>
    @media screen and (min-width: 1000px){
        .controlArea{
            display: inline-block;
            vertical-align: middle;
        }       
    }
    .controlArea{
        /* background-color: white; */
        width: 300px;
        height: 250px;    
        margin-left: 100px;
    }
    .wrap{
        position: relative;
        top: 40px;
    }
    .direct{
        display: block;
        margin: auto;
        text-align: center;
        line-height: 4.5;
        margin-bottom: 10px;
    }
    .downArea{
        margin: auto;
        text-align: center;
    }
    .downArea > div{
        display: inline-block;
        margin: 10px;
        line-height: 4.5;
    }
    .direct, .left, .right, .stop{
        box-shadow: 1px 1px 10px 2px white;
        width: 80px;
        height: 70px;
        color: rgb(179, 145, 35)
    }
    .key:hover{
        cursor: pointer;
        background-color: black;
        color: white;
    }
    .name{
        color: red;
    }
</style>