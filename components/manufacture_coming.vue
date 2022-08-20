<template>
    <div class="card_main_">
        <div class="warning">
            <div>
                Правила заполнения: 
                <br>
                1.Форма строго по образцу
                <br>
                2.Длина в миллиметрах
                <br>
                3.Количество в штуках
                <br>
                4.Вес в килограммах
                <br>
                5.Цена как в накладной
            </div>
        </div>
        <div class="row_area">
            <div class="main_row w" v-for="(need, index) in needed" :key="index">
                <div @click="delete_position(need)">
                    <i class="fas fa-times-circle icons_hover" title="Удалить позицию"></i>
                </div>
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
                <input class="row_input number_input" placeholder="Длина, мм." type="number" v-model="need.length">
                <input class="row_input number_input" placeholder="Количество, м." type="number" v-model="need.count">
                <div class="plus_minus" @click="get_pars(0, need)" title="Рассчитать количество">
                    <i class="fas fa-calculator"></i>
                </div>
                <input class="row_input number_input" placeholder="Вес, кг." type="number" v-model="need.weight">
                <div class="plus_minus" @click="get_pars(1, need)" title="Рассчитать вес">
                    <i class="fas fa-weight"></i>
                </div>
                <input class="row_input number_input" placeholder="Цена, руб." type="number" v-model="need.price">
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
import { mapActions } from 'vuex'
export default ({

    props:['data'],

    data(){
        return{
            needed:[{
                material:'',
                count:null,
                form: '',
                length: '',
                weight: '',
                price: '',
            }],
            forms:[],
            filtered_forms:[],
            filtered_materials:[],
            materials:[],
            url: this.$store.getters.api_url_new,
            user: this.$store.getters.USER_ID
        }
    },

    computed:{
        
    },

    mounted(){
        this.get_forms_and_materials()
    },

    methods:{

        ...mapActions([
            'select'
        ]),
        
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
            if(!this.check_data()) return this.$alert('Заполните поля', 'is-warning')
            if(this.needed.length == 0) return this.$alert('Добавьте покупную и попробуйте снова', 'is-warning')
            this.$_send_({cmd: "metall_storage_arrival", metall: JSON.stringify(this.needed), user:this.user}, response=>{
                if(response.data.result == "-ok"){
                    this.$alert("Металл успешно оприходован", 'is-success')
                    this.$parent.obj.isactive = false
                    this.$emit('update')
                }else{
                    this.$alert("Не удалось оприходовать металл, попробуйте позже", 'is-warning')
                }
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
        },

        get_pars(par, metall){
            if(metall.form == '' || metall.count == "" || metall.material == "") return this.$alert('Заполните необходимые поля', 'is-warning')
            this.$_send_({cmd:"metall_arrival_get_pars", par: par, metall: JSON.stringify(metall)}, response=>{
                if(par == 0){
                    let options = []
                    response.data.forEach(el => {
                        // options.push({name:el.res, })
                        el.name = el.res
                        options.push(el)
                    });
                    // доделать с новым селектом
                    this.select({
                        items: options,
                        set_action: (option)=>{
                            metall.length = option.length
                            metall.count = option.count
                        },
                    })
                }else{
                    metall.weight = response.data
                }
            })
        },
        
        set_width(par){}
    }
})
</script>

<style scoped>
    .card_main_{
        display: flex;
        flex-direction: column;
        height: 450px;
        width: 1080px;
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
    .warning{
        color: red;
        width: 100%;
        padding-left: 10px;
        font-weight: 600;
    }
</style>