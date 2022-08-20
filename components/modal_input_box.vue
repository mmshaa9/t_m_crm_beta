<template>
    <div class="main_change">
        <div class="select_ main_change_select">
            <label>{{data.label}}</label>
            <input ref="box_input" class="tm_input" v-model="data.name" :type="data.type ?? 'text'" @keydown.enter.prevent="save" :placeholder="'Введите ' + data.label.toLowerCase()">
        </div>
        <div class="main_change_buttons">
            <button class="button" @click="save">
Сохранить
</button>
            <button class="button" @click="data.isactive = false">
Отмена
</button>
        </div>
    </div>
</template>

<script>
module.exports = {
    props:['data'],
    data(){
        return{

        }
    },

    mounted(){
        this.$nextTick( ()=>{	
            
            this.$refs.box_input.focus()
        })
    },

    methods:{
        save(){
            this.$refs.box_input.blur()
            this.$emit('save')
        }
    }
}
</script>

<style scoped>
    .main_change{
        height: 200px;
        width: 500px;
        padding: 5px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
    }

    .main_change_text {
        margin-bottom: 10px;
        border: 1px solid gray;
        padding: 5px;
        font-size: 14px;
    }
    .main_change_buttons{
        display: flex;
        justify-content: flex-end;
    }
    .select_.main_change_select {
        display: flex;
    }
    .main_change_select>label{
        width: 25%;
    }

    .main_change_buttons>*{
        margin: 0px;
        height: 30px;
    }
</style>