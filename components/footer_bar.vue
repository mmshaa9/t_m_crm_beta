<template>
    <div class="minimize" ref="bottomBar" @mouseover="toggle_bar()" @mouseout = "toggle_bar()"> 
        <transition-group tag="div" name="slide-prev">
            <div v-for="element in elements" :key="element.item_id">
                <!--  -->
                <div :class="{'opened_tab': element.isactive}" @click="open(element)">
ID{{element.item_id}}<i class="far fa-window-maximize"></i>
</div>
            </div>
        </transition-group>
    </div>   
</template>

<script>
export default ({
    props:['elements'],
    data(){
        return {
            mini: true,
            items:[],
        }
    },

    created(){
        this.$emit('minimize', this.minimize)
    },

    methods: {
        toggle_bar: function(){
            if(this.mini){
                this.$refs.bottomBar.style.height = 40 + 'px'
                this.$refs.bottomBar.style.backgroundColor = '#78909cb8'
                this.mini = false
            }else{
                this.$refs.bottomBar.style.height = 10 + 'px'
                this.$refs.bottomBar.style.backgroundColor = '#cfd8dccc'
                this.mini = true
            }
        },

        minimize: function(){
            console.log('saaaaas')
        },

        open: function(element){
            element.isactive = true
        }
    }
})
</script>

<style scoped>

.minimize{
    display: flex;
    position: absolute;
    top: auto;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: #cfd8dccc;
    transition: all 0.25s ease-in-out 0s;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 6px 20px rgba(0, 0, 0, 0.23);
    justify-content: center;
    min-height: 10px;
}

.minimize>*{
    display: flex;
}

.minimize:hover{
    /* background: rgb(223 223 223); */
}

.minimize>*>*{
    margin: 5px;
    width: 125px;
    text-align: center;
    border: 1px solid gray;
    border-radius: 4px;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
    cursor: pointer;
    overflow: hidden;
}

.minimize>*>*:hover{
    background-color: papayawhip;

}

.opened_tab{
    background-color: papayawhip;
}

.fa-window-maximize{
    padding-left: 7px;
}
</style>