<template>
    <div class="selectRobot">
        <label for="robot_id" class="robot_title">Robot{{ robot_id }} name:</label>
        <input type="text" v-model="robot_name" id="robot_id">
        <font-awesome-icon icon="angle-left" size="2x"  class="left" @click="asc_id"/>
        <font-awesome-icon icon="angle-right" size="2x" class="right" @click="inc_id"/>
    </div>
</template>



<script>
   export default {
       data(){
           return {
               robot_name: 'burger1',
               robot_id: 1,
               view_controls: [
                    { id: 0, view_sub_name: '/burger1/image', vel_sub_name: '/burger1/cmd_vel', active: false},
                ]
           }
       },
       methods: {
           asc_id(){
               if(this.robot_id>0){
                   this.robot_id--
               }
           },
           inc_id(){
               this.robot_name=`burger${++this.robot_id}`
           }
       },
       watch: {
           robot_name: {
               deep:true,
               handler() {
                this.view_controls[this.robot_id-1] = { 
                    id: this.robot_id-1, 
                    view_sub_name: `/${this.robot_name}/image`, 
                    vel_sub_name: `/${this.robot_name}/cmd_vel`, 
                    active: false
                }
                this.$emit('update', this.view_controls)
               },
           }
       },
       mounted(){
           console.log(this.view_controls);
            this.$emit('update', this.view_controls)
       }
   }
</script>


<style scoped>

    .selectRobot{
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        width: 300px;
        backdrop-filter: blur(2px);
        border: 1px solid black;
        box-shadow: 0px 0px 40px black;
        padding: 20px 0px;
        position: fixed;
        bottom: 46px;
    }
    .robot_title{
        width: 50%;
        color: white;
        margin-bottom: 5px;
    }
    #robot_id{
        width: 50%;
        padding: 5px;
        border-radius: 5px;
        border: 2px solid transparent;
        font-size: 16px;
    }
    #robot_id:focus{
        outline-style: none;
        border: 2px solid green;
    }
    .left{
        position: absolute;
        top: 40px;
        left: 15px;
        color: white;
    }
    .left:hover{
        cursor: pointer;
        color: green;
    }
    .right{
        position: absolute;
        top: 40px;
        right: 15px;
        color: white;
    }
    .right:hover{
        cursor: pointer;
        color: green;
    }
</style>