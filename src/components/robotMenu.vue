<template>
    <div class="menu">
        <div class="item touch" @click="connectToggle">
                <span class="light" :class="status"></span> 
            <span>Connect</span>
        </div>
        <div class="item touch" @click="selectRobotToggle"><span>Select robot</span>
           
        </div>
        <div class="item" :class="{touch: this.isActive, disabled: this.disabled}" @click="controlToggle"><span>Control</span></div>
        <div class="item" :class="{touch: this.isActive, disabled: this.disabled}" @click="viewToggle"><span>View</span></div>
        
        <!-- <div class="item"><span>task</span></div> -->
        
    </div>
    <transition name="connect">
        <robotConnect v-show="connectShow" @update="updateConnectInfo"/>
    </transition>

     <transition>
        <RobotSelect v-if="selectRobotShow" @update="updateControlInfo"/>
    </transition>
    
   
        <div v-for="unit in view_controls" :key="unit">
            <div :class="{view_control_active: unit.active && viewShow && controlShow, unit}" @click="changeControl(unit.id)">
                <transition name="view">
                        <robotCamera v-if="viewShow" :ros="ros" :ROSLIB="ROSLIB" :view_sub_name="unit.view_sub_name"/>
                </transition>
                <transition name="control">
                    <robotControl v-if="controlShow" :ros="ros" :ROSLIB="ROSLIB" :vel_sub_name="unit.vel_sub_name" :id="unit.id" :active="unit.active"/>
                </transition>
            </div>
        </div>

</template>

<script>
import robotConnect from '@/components/robotConnect.vue'
import robotControl from '@/components/robotControl.vue'
import robotCamera from '@/components/robotCamera.vue'
import robotSelect from '@/components/robotSelect.vue'
import ROSLIB from 'roslib'
import RobotSelect from '@/components/robotSelect.vue'

export default {
    name: 'robotMenu',
    components: {
    robotConnect,
    robotControl,
    robotCamera,
    robotSelect,
    RobotSelect
},
    data() {
        return {
            connectShow: false,
            selectRobotShow: false,
            controlShow: false,
            viewShow: false,
            isActive: false,
            disabled: true,
            ros: null,
            ROSLIB: ROSLIB,
            robotType: '',
            status: 'orange-light',
            view_controls: null
        }
    },
    methods:{
        connectToggle(){
            this.connectShow = !this.connectShow
        },
        selectRobotToggle(){
            this.selectRobotShow = !this.selectRobotShow
        },
        controlToggle(){
            if(this.isActive)this.controlShow = !this.controlShow
        },
        viewToggle(){
            if(this.isActive)this.viewShow = !this.viewShow
        },
        updateConnectInfo(data){
            this.robotType = data.robotType
            this.ros = new ROSLIB.Ros({
                url : `ws://${data.IP}:${data.Port}`
            })
            this.ros.on('connection', ()=>{
                this.status = 'green-light'
                this.disabled = false
                this.isActive = true
            })
            this.ros.on('error', ()=>{
                this.status = 'red-light'
            })
            this.ros.on('close', ()=>{
                this.status = 'orange-light'
            })
            this.connectShow = false
        },
        updateControlInfo(data){
            this.view_controls = data
        },
        changeControl(id){
            for(let i=0;i<this.view_controls.length;i++){
                this.view_controls[i].active = false
            }
            this.view_controls[id].active = true
        },
    }
}
</script>
<style scoped>
.menu {
    display: flex;
    position: fixed;
    bottom: 0;
    background-color: rgb(19, 77, 110);
    width: 100%;
    font-size: 0px;
    border-radius: 10px 10px 0px 0px;
    box-shadow: 0px -1px 10px 0.5px rgba(0, 0, 0, 0.8);
    color: wheat;
}

.item {
    width: 100%;
    padding: 12px 8px;
    font-size: 18px;
    text-align: center;
}
.item > span{
    vertical-align: middle;
}
.touch:hover{
    transition: .3s;
    background-color: rgb(235, 43, 36);
    color: rgb(5, 0, 0);
    border-radius: 5px;
    cursor: pointer;
}

.disabled{
    background-color: rgba(207, 191, 191, 0.199);
}
.not-view{
    color: red;
    margin: 10px;
}
.view_control_active{
    border: 2px solid greenyellow;  
}
.unit{
    margin: 10px;
}
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
@keyframes red-flash{
    0%{
        background-color: rgb(202, 84, 16, 0);
    }
    50%{
        background-color: rgb(202, 28, 16);
    }
    100%{
        background-color: rgb(202, 84, 16, 0);
    }
}
@keyframes green-flash{
    0%{
        background-color: rgb(202, 84, 16, 0);
    }
    50%{
        background-color: rgb(26, 214, 19);
    }
    100%{
        background-color: rgb(202, 84, 16, 0);
    }
}
.light{
    display: inline-block;
    padding: 8px;
    border-radius: 50%;
    vertical-align: middle;
    margin-right: 10px; 
}
.orange-light{
    animation: orange-flash 2s infinite;
}
.green-light{
    animation: green-flash 2s infinite;
}
.red-light{
    animation: red-flash 2s infinite;
}
.v-enter-active{
    animation: orangeFlash .8s infinite;
}
.connect-enter-active,.connect-leave-active,
.view-enter-active,.view-leave-active,
.control-enter-active,.control-leave-active
{
    transition: opacity .5s ease; 
}

 .connect-enter-from,.connect-leave-to,
 .view-enter-from,.view-leave-to,
 .control-enter-from,.control-leave-to
 {
    opacity: 0;
 }
 
</style>