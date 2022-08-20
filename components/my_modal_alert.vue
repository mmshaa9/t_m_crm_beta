<template>
    <div v-show="data.visible" class="alert_wrapper">
        <div class="alert_block">
            <div class="alert_text" v-html="data.text"></div>
            <button class = "button" @click="data.visible=false">
Ok
</button>
        </div>                
    </div>
</template>

<script>
    export default {
        props:['data'],
        data: function() {
            return {                
              
            }
        },
    }
</script>

<style scoped>
    .alert_wrapper{
        position:absolute;
        top:0;
        left:0;
        width:100%;
        height:100%;        
        background:rgba(0,0,0,0.1);
        display:flex;    
        flex-direction: column;        
    }

    .alert_block{
        margin:auto;
        padding:10px;
        text-align:center;
        background:lightgray;        
        box-shadow: 0px 0px 20px 0px gray;
        box-shadow: 0px 0px 20px 10px gray;        
        z-index: 100;
        width:25%;
        max-width:50%;

        display:flex;
        flex-direction:column;
    }

    .alert_text{            
        margin:5px;
        padding:15px;
        font-size:10pt;
        background:rgb(245,245,245);
        background:rgb(0, 0, 0, 0.05);
        border-radius:5px;
    }

    button{    
        padding:5px;
        margin:5px;
        border:1px solid gray;        
        font:inherit;        
        padding: 5px 20px;           
    }

    button:active{        
        box-shadow: inset 0px 0px 2px 1px gray;
    }
</style>