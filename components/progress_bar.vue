<template>
<div class="ses">
    <transition name="zoom-in">
        <div class="progress_back" v-if="progress.isactive">
            <div class="load_container mat_des_shadow">
                <div class="show_progress_logo">
                    <div class="hid" ref="loaded"></div>
                    <div class="color_logo">

                    </div>
                </div>
                <div class="progress_area">
                    {{progress.status}}%
                    <b-button size="is-small" @click="$emit('cancel')" v-if="progress.cancel_need == undefined ? true : false">
                        Отмена
                    </b-button>
                </div>
            </div>
        </div>
    </transition>
</div>
</template>

<script>
module.exports = ({
    props: ['progress'],
    data(){
        return{
            //logo_url:'frontend/img22/logo.png',
        }
    },

    computed:{
        get_load_stat: function(){
            return this.progress.status
        }
    },

    watch:{
        get_load_stat(){
            if(this.progress.isactive){
                this.load_progress()
            }
        }
    },

    methods:{
        load_progress: function(){
            console.log(1)
            if(this.$refs.loaded) this.$refs.loaded.style.height = 109 - this.progress.status + "px"
            // this.$refs.loaded.style.background =  "linear-gradient(360deg, rgb(0 0 0 / 0%) 0%, lightgray " + this.progress.status + "%, lightgray " + this.progress.status + "%, lightgray 100%)";
        }
    }
})
</script>

<style>

.progress_logo {
    filter: drop-shadow(0 0 2px black) grayscale(100%) contrast(0%) opacity(0.99);
    width: 90px;
    background-image: url('~@/assets/logo.png');
    height: 1px;
    background-size: cover;
}

.show_progress_logo{
    width: 90px;
    height: 110px;
}

.load_container{
    width: 190px;
    height: 190px;
    background: lightgray;
    border: 0px solid lightgray;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    border-radius: 10px;
    padding-top: 10px;
}

.hid{
    position: absolute;
    width: 90px;
    background: url('~@/assets/logo.png');
    height: 109px;
    background-size: cover;
    filter:  grayscale(100%) opacity(0.99);
    z-index: 1;    
    /* transition: all 1s ease-in-out 0s; */
    /* transition: height 0.1s; */
}

.color_logo{
    width: 90px;
    background-image: url('~@/assets/logo.png');
    height: 109px;
    background-size: cover;
    position: absolute;
    filter: drop-shadow(0px 0px 1px black);
}

.progress_back{
    background: rgba(0,0,0,.5);
    z-index: 100;
    width: 100%;
    height: 100%;
    bottom: 0;
    left: 0;
    position: absolute;
    right: 0;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
}

.progress_area{
    display: flex;
    flex-direction: column;
    align-items: center;
    border-radius: 5px;
    overflow: hidden;
    font-weight: bold;
}

.progress_text{
    padding: 20px;

}

.alert_button{
    justify-content: flex-end;
    padding: 20px;
    display: flex;
    align-items: center;
    background-color: #f5f5f5;
}
</style>