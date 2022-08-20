<template>
    <div class="card_main_">
        <div class="row_area">
            <!-- <div class="main_row_header bold">
                <div><i class="fas fa-times-circle"></i></div>
                <div>Наименование</div>
                <div>Кол.</div>
            </div> -->
            <div class="main_row w" v-for="(buy, index) in buyed" :key="index">
                <div @click="delete_position(buy)">
<i class="fas fa-times-circle icons_hover" title="Удалить позицию"></i>
</div>
                <input class="row_input" placeholder="Наименование"  v-model="buy.name">
                <input class="row_input number_input" placeholder="Количество" type="number" v-model="buy.count">
                <div class="plus_minus" @click="minus(buy)">
<i class="fas fa-minus icons_hover"></i>
</div>
                <div class="plus_minus" @click="plus(buy)">
<i class="fas fa-plus icons_hover" ></i>
</div>
            </div>
            <div class="add_button" @click="add_position">
<i  class="fas fa-plus-circle icons_hover" title="Добавить позицию"></i>Добавить позицию
</div>
        </div>
        <div class="action_button">
            <b-button size="is-small" @click="save_buyed">
Сохранить
</b-button>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default ({

    props:['data'],

    data(){
        return{
            buyed:[{
                name:'',
                count:''
            }],
            url: this.$store.getters.api_url_new,
        }
    },

    methods:{
        add_position: function(){
            this.buyed.push({name:'', count:''})
        },

        delete_position: function(par){
            this.buyed.splice(this.buyed.indexOf(par), 1)
        },

        save_buyed: function(){
            if(!this.check_data()) return app.alert('Заполните поля', 'is-warning')
            if(this.data.item_id == '') return app.alert('Не указан ID изделия, перезагрузите страницу', 'is-warning')
            if(this.buyed.length == 0) return app.alert('Добавьте покупную и попробуйте снова', 'is-warning')
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'constructors_item_add_buyed')
            bodyFormData.append('items', JSON.stringify(this.buyed))
            bodyFormData.append('item_id', this.data.item_id)
            bodyFormData.append('user', this.data.cur_user)
            axios({
                    method: 'post',
                    url: this.url,
                    data: bodyFormData,
                    headers: {'Content-Type': 'multipart/form-data' }
                    }) 
            .then((response)=>{
                if(response.data.result == "-ok"){
                    this.data.isactive = false
                    this.$alert('Комплектующие добавлены', 'is-success')
                }else{
                    this.$alert('Не удалось добавить комплектующие, попробуйте позже.', 'is-warning')
                } 
                console.log(response.data)
            })
        },

        plus: function(par){
            par.count++
        },

        minus: function(par){
            par.count--
            if(par.count <=0){
                par.count = 0
            }
        },

        check_data: function(){
            return this.buyed.every( el => el.name != '' && el.count !=0)
        }
    }
})
</script>

<style scoped>
    .card_main_{
        display: flex;
        flex-direction: column;
        height: 450px;
        width: 600px;
        align-items: flex-end;
        justify-content: space-between;
    }

    .row_area{
        width: 100%;
        padding: 5px;
        height: 100%;
        overflow: auto;
        background: whitesmoke  ;
        border-bottom: 1px solid lightgray;
        border-top: 1px solid lightgray;
        padding-top: 10px;
    }

    .main_row{
        display: flex;
        align-items: center;
        border-bottom: 1px solid gray;
        padding-bottom: 6px;
        margin-bottom: 5px;
        padding-left: 5px;
    }

    .main_row_header{
        display: flex;
        align-items: center;
        padding-left: 5px;
    }

    .main_row_header>*:nth-child(2){
        margin-right: 10px;
        margin-left: 10px;
        flex-grow: 1;
    }

    .main_row_header>*:last-child{
        margin-right: 10px;
        width: 50px;
    }

    .main_row>input:nth-child(2){
        margin-right: 10px;
        margin-left: 10px;
        flex-grow: 1;
    }

    .main_row>input:nth-child(3){
        margin-right: 10px;
        width: 100px;
    }
    
    .main_row>*:nth-child(4){
        margin-right: 5px;
    }

    .row_input{
        border: 1px solid lightgray;
        padding-left: 5px;
        outline: none;
        min-height: 30px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        word-break: break-all;
    }

    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
        -webkit-appearance: none;
        margin: 0;
    }
    
    .icons_hover{
        cursor: pointer;
    }

    
    .add_button {
        display: flex;
        align-items: center;
        padding: 5px;
        border-radius: 5px;
        transition: all 0.25s ease-in-out 0s;
    }

    .add_button > * {
        margin-right: 5px;
    }

    .add_button:hover {
        cursor: pointer;
        box-shadow: rgb(0 0 0 / 16%) 0px 3px 6px, rgb(0 0 0 / 23%) 0px 3px 6px;
        background: whitesmoke;
    }

    .plus_minus{
        border: 1px solid lightgray;
        min-height: 30px;
        min-width: 30px;
        background: white;
        display: flex;
        justify-content: center;
        align-items: center;
    }
</style>