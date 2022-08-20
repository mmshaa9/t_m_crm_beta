<template>
    <div v-show="is_offline" class="offline_warning">
        <div v-if="connection_error">
Нет связи с сервером
</div>
        <div v-else>
Отсутствует подключение к интернету
</div>				
    </div>
</template>

<script>
module.exports = {
    props:['connection_error'],
    data: function(){
        return {
            offline: false,
            message:''
        }
    },

    created: function(){
        var t = this    
        window.ononline = function(){
            console.log("offline-indicator: online")    
            t.offline = false            
        }

        window.onoffline = function(){
            console.log("offline-indicator: offline")            
            t.offline = true
        }
    },

    computed: {
        is_offline: function(){
            return this.offline || this.connection_error
        }
    }
};

</script>

<style scoped>
.offline_warning{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.8);				
    z-index: 100;
    display:flex;
    flex-direction: column;
    justify-content: center;
    align-content: center;
}
.offline_warning>*{
    
    margin: auto;
    text-align: center;
    color: white;
    font-size: 50pt;
    font-weight: bold;
}
</style>
