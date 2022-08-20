<template>
    <div v-if="norm_text.length > length">
        <span class="ses" :class="{not_cutted_text:cuted_text.length == norm_text.length}">{{cuted_text}}</span>
        <span class="show_all_text" v-if="cuted_text.length != norm_text.length" @click.stop="show">{{show_text}}</span>
        <span v-else @click.stop="hide" class="show_all_text">Скрыть</span>
    </div>
    <div v-else>
        <span >{{norm_text}}</span>
    </div>
</template>

<script>
export default ({
    props: ['text', 'length', 'show_text'],
    data(){
        return{
            cuted_text:'',
            norm_text:'',
        }
    },

    computed:{
        cut_text: function(){
            return this.text.substring(0, this.length)
        },

        normal_text: function(){
            return this.text
        }
    },

    mounted(){
        this.cuted_text = this.cut_text
        this.norm_text = this.normal_text
    },

    methods:{
        show: function(){
            this.cuted_text = this.norm_text
        },

        hide: function(){
            this.cuted_text = this.norm_text.substring(0,  this.length)
        }
    }
})



</script>

<style scoped>
.show_all_text {
    font-size: small;
    color: #9b9bff;
    cursor: pointer;
    margin-left: 5px;
    margin-right: 5px;
}

.show_all_text:hover {
    cursor: pointer;
    color: #b4b4ff;
    text-decoration: underline;
}

.not_cutted_text{
    white-space: break-spaces;
}
</style>