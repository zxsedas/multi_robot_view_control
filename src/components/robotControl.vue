<template>
    <div class="controlArea">
        <h3 class=name>Robot{{ this.id }}</h3>
        <div class="wrap" >
            <div class="key direct" :class="flash_active[0] ? keyflash : ''" @click="direct">W</div>
            <div class="downArea">
                <div class="key left" :class="flash_active[1] ? keyflash : ''" @click="left">A</div>
                <div class="key stop" :class="flash_active[2] ? keyflash : ''" @click="stop">S</div>
                <div class="key right" :class="flash_active[3] ? keyflash : ''" @click="right">D</div>
            </div>   
            <div class="param">
             <label for="rate" class="vel_title">linear velocity rate: {{ twist.linear.x }}</label>
            <input type="number" v-model="this.lin_vel_rate" v-on:input="fvel" id="rate" min="0" max="0.22">
            <label for="rate" class="vel_title">angular velocity rate: {{ twist.angular.z }}</label>
            <input type="number" v-model="this.ang_vel_rate" v-on:input="fvel" id="rate" min="0" max="2.84">
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
            flash_active: [false, false, false, false], //direct,left,stop,right
            keyflash: 'orange-flash',
            lin_vel_rate: 0.01,
            ang_vel_rate: 0.01,
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
        fvel(){
            this.lin_vel_rate = Number(this.lin_vel_rate)
            this.ang_vel_rate = Number(this.ang_vel_rate)
        },
        direct(){
            this.changeKeyFalsh(0)
            if(this.twist.linear.x <= 0.22){
                this.twist.linear.x += this.lin_vel_rate
                this.twist.linear.x = Number(this.twist.linear.x.toFixed(2))
            }       
            this.cmdPublisher.publish(this.twist) 
           
        },
        left(){
            this.changeKeyFalsh(1)
            if(this.twist.angular.z <= 2.84){
                this.twist.angular.z += this.ang_vel_rate
                this.twist.angular.z = Number(this.twist.angular.z.toFixed(2))
            }
            this.cmdPublisher.publish(this.twist) 
        },
        right(){
            this.changeKeyFalsh(3)
            if(this.twist.angular.z >= -2.84){
                this.twist.angular.z -= this.ang_vel_rate
                this.twist.angular.z = Number(this.twist.angular.z.toFixed(2))
            }
            this.cmdPublisher.publish(this.twist) 
        },
        stop(){
            this.changeKeyFalsh(2)
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
        changeKeyFalsh(id){
            for(let i=0;i<4;i++)this.flash_active[i]=false
            this.flash_active[id] = true
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
    @keyframes orange-flash{
        0%{
            background-color: rgb(202, 84, 16, 0);
        }
        50%{
            background-color: rgb(202, 84, 16, 1);
        }
        100%{
            background-color: rgb(202, 84, 16, 0);
        }
    }
    .orange-flash{
        animation: orange-flash 2s infinite;
    }
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
        position: relative;
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
    .param{
        width: 300px;
        position: absolute;
        top: 0;
        right: -350px;
        display: flex;
        flex-wrap: wrap;
    }
    .vel_title{
        width: 100%;
        color:white;
        font-size: 18px;
    }
    #rate{
        width: 50%;
        padding: 4px 6px;
        font-size: 18px;
    }

</style>