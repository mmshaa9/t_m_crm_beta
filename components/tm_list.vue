<template>
    <div class="list_row">
        <slot name="list_style"></slot>
    </div>
</template>

<script>
    module.exports = ({
        data(){
            return {
                list_active: undefined,
            }  
        }, 
        
       methods:{
         
       }

       
    })
</script>

<style scoped>
    
    .list_row{
        display: flex;
        flex-direction: column;
        flex-grow: 1;
        /* align-items: center; */
        background: papayawhip;
        margin-bottom: 5px;
        position: relative;
        border: 1px solid lightgray;
        border-radius: 10px;
        box-shadow: rgb(0 0 0 / 12%) 0px 1px 3px, rgb(0 0 0 / 24%) 0px 1px 2px;
        cursor: pointer;
    }
    .list_row:hover{
        box-shadow: 0 3px 6px rgb(0 0 0 / 16%), 0 3px 6px rgb(0 0 0 / 23%);
        transition: all 0.25s ease-in-out 0s;
        /* background: floralwhite; */
    }
    
</style>