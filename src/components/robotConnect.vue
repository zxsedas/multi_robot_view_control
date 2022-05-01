<template>
    <div id="connect">
            <label for="RobotType">RobotType:</label>
            <input type="text" v-model="connectInfo.robotType">
            <label for="IP">IP:</label>
            <input type="text" v-model="connectInfo.IP">
            <label for="Port">Port:</label>
            <input type="text" v-model="connectInfo.Port">
            <button @click="submitData" id="submit">connect</button>
    </div>
</template>

<script>
export default {
    name: 'robotConnect',
    data(){
        return {
            connectInfo: {
                robotType: 'burger',
                IP: 'localhost',
                Port: 9090
            }
        }
    },
    methods: {
        submitData(){
            this.$emit('update', this.connectInfo)
        }
    }
}
</script>
<style scoped>
#connect{
    width: 280px;
    height: 30%;
    display: flex;
    flex-wrap: wrap;
    padding: 10px;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    backdrop-filter: blur(2px);
    border: 1px solid black;
    box-shadow: 0px 0px 40px black;
}
#connect > label{
    color: wheat;
}
#connect > input{
    width: 100%;
    margin: 6px 0px;
    font-size: 16px;
}
#submit{
    margin: auto;
    padding: 6px 10px;
}
</style>