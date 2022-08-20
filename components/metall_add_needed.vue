<template>
    <div class="card_main_">
        <div class="row_area">
            <!-- <div class="main_row_header bold">
                <div><i class="fas fa-times-circle"></i></div>
                <div>Наименование</div>
                <div>Кол.</div>
            </div> -->
            <div class="main_row w" v-for="(need, index) in needed" :key="index">
                <div @click="delete_position(need)">
<i class="fas fa-times-circle icons_hover" title="Удалить позицию"></i>
</div>
                <!-- <select class="row_input" placeholder="Форма"  v-model="need.form">
                    <option v-for="form in forms" :key="form">{{form}}</option>
                </select>
                <select class="row_input" placeholder="Материал"  v-model="need.material">
                    <option v-for="material in materials" :key="material.Материал">{{material.Материал}}</option>
                </select> -->
                <b-autocomplete
                    v-model="need.form"
                    :data="filtered_forms"
                    size="is-small"
                    placeholder="Форма"
                    open-on-focus
                    @typing="get_filtered_forms"
                    @focus="get_filtered_forms(need.form)"
                    @select="option=>select_opt(need, option, 'form')">
                    <template #empty>
Не найдено совпадений
</template>
                </b-autocomplete>
                <b-autocomplete
                    v-model="need.material"
                    :data="filtered_materials"
                    size="is-small"
                    placeholder="Материал"
                    open-on-focus
                    @typing="get_filtered_materials"
                    @focus="get_filtered_materials(need.material)"
                    @select="option=>select_opt(need, option, 'mat')">
                    <template #empty>
Не найдено совпадений
</template>
                </b-autocomplete>
                <input class="row_input number_input" placeholder="Количество, м." type="number" v-model="need.count">
                <div class="plus_minus" @click="minus(need)">
<i class="fas fa-minus icons_hover"></i>
</div>
                <div class="plus_minus" @click="plus(need)">
<i class="fas fa-plus icons_hover" ></i>
</div>
            </div>
            <div class="add_button" @click="add_position">
<i  class="fas fa-plus-circle icons_hover" title="Добавить позицию"></i>Добавить позицию
</div>
        </div>
        <div class="action_button">
            <b-button size="is-small" @click="save_needed">
Сохранить
</b-button>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import * as my_f from "@/my"
export default ({

    props:['data'],

    data(){
        return{
            needed:[{
                material:'',
                count:null,
                form: ''
            }],
            forms:[],
            filtered_forms:[],
            filtered_materials:[],
            materials:[],
            url: this.$store.getters.api_url_new,
        }
    },

    computed:{
        
    },

    mounted(){
        this.get_forms_and_materials()
    },

    methods:{
        add_position: function(){
            this.needed.push({material:'', count:null, form: ''})
        },

        delete_position: function(par){
            this.needed.splice(this.needed.indexOf(par), 1)
        },

        get_forms_and_materials(){
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'metall_get_forms_and_materials')
            axios({
                    method: 'post',
                    url: this.url,
                    data: bodyFormData,
                    headers: {'Content-Type': 'multipart/form-data' }
                    }) 
            .then((response)=>{
                this.forms = response.data.forms
                response.data.materials.forEach(el => {
                    this.materials.push(el.Материал)
                }); 
                this.get_filtered_forms('')
                this.get_filtered_materials('')
                console.log(response.data)
            })  
        },

        
        get_filtered_forms(text) {
            this.filtered_forms =  this.forms.filter((option) => {
                return option
                    .toString()
                    .toLowerCase()
                    .indexOf(text.toLowerCase()) >= 0
            })
        },

        
        get_filtered_materials(text) {
            this.filtered_materials = this.materials.filter((option) => {
                return option
                    .toString()
                    .toLowerCase()
                    .indexOf(text.toLowerCase()) >= 0
            })
        },

        save_needed(){
            if(!this.check_data()) return app.alert('Заполните поля', 'is-warning')
            if(this.needed.length == 0) return app.alert('Добавьте покупную и попробуйте снова', 'is-warning')
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'metall_add_needed')
            bodyFormData.append('items', JSON.stringify(this.needed))
            bodyFormData.append('user', this.data.user)
            axios({
                    method: 'post',
                    url: this.url,
                    data: bodyFormData,
                    headers: {'Content-Type': 'multipart/form-data' }
                    }) 
            .then((response)=>{
                if(response.data.result == "-ok"){
                    this.data.isactive = false
                    this.data.updated = !this.data.updated
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
            return this.needed.every( el => el.count != '' && el.count !=0 && el.count != null)
        },

        select_opt(need, option, par){
            if(par == 'form'){
                need.form = option
            }else{
                let material = option
                need.material = material
            }
        }
    }
})
</script>

<style scoped>
    .card_main_{
        display: flex;
        flex-direction: column;
        height: 450px;
        width: 800px;
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

    .main_row>*{
        margin-right: 5px;
    }

    .main_row>.autocomplete>.control>input{
        border: 1px solid lightgray;
        padding-left: 5px;
        outline: none;
        min-height: 30px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        word-break: break-all;
        color: black;
        width: 250px;
    }

    .main_row>.autocomplete>.control>input::placeholder{
        color:  #8e8e8e;
        font-size: unset;
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