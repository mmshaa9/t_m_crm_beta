<template>
    <div class="shield_card">
        <div class="selector_row">
            <label for="plazma" class="field_label">Обозначение</label>
            <input id="plazma" v-model="shield.name" class="input_s">
        </div>
        <div class="selector_row">
            <label for="tokarka" class="field_label">Производительность</label>
            <input id="tokarka" v-model="shield.perfomance" class="input_s">
        </div>
        <div class="selector_row">
            <label for="metall" class="field_label">Двигатель, мощность</label>
            <input id="metall" v-model="shield.power" class="input_s">
        </div>
        <div class="selector_row">
            <label for="raspil" class="field_label">Обороты</label>
            <input id="raspil" v-model="shield.rpm" class="input_s">
        </div>
        <div class="selector_row">
            <label for="constructor" class="field_label">Номер</label>
            <div class="shield_num">
                <input :disabled="shield.number1 != '' && shield.number1 != null "  id="constructor" v-model="shield.number1" class="input_s"> - 
                <input :disabled="shield.number1 != '' && shield.number1 != null " id="constructor" v-model="shield.number2" class="input_s">
                <b-button :disabled="shield.number1 != '' && shield.number1 != null " size="is-small" class="cancel_button" @click="add_shieldik_confirm">
Ш
</b-button>
            </div>
        </div>
        <div class="selector_row">
            <label for="shield" class="field_label">Масса</label>
            <input id="shield" v-model="shield.weight" class="input_s">
        </div>
        <div class="selector_row">
            <label for="project_end" class="field_label">Месяц</label>
            <input :disabled="shield.month != '' && shield.month != null" id="project_end" v-model="shield.month" class="input_s">
        </div>
        <div class="selector_row">
            <label for="category" class="field_label">Год</label>
            <input :disabled="shield.year != '' && shield.year != null" id="category" v-model="shield.year" class="input_s">
        </div>
        <div class="bottoms_buttons">
            <div>
                <b-button size="is-small" @click="png_create">
Сохранить в PDF
</b-button>
            </div>
            <b-button size="is-small" @click="save_shield">
Сохранить
</b-button>
            <b-button size="is-small" @click="data.isactive = false" class="cancel_button">
Отмена
</b-button>
        </div>
    </div>
</template>

<script>
import * as my_f from "@/my"
import axios from 'axios'
import { mapActions } from 'vuex'

export default ({

    props: ['data'],
    data: function(){
        return{
            url: this.$store.getters.api_url_new,
            shield:{
                // name:'',
                // perfomance:'',
                // power:'',
                // rpm:'',
                // number1:'',
                // number2:'',
                // height:'',
                // month:'',
                // year:''
            },
        }
    },

    computed: {
        id(){
            return this.data.item_id
        }
    },

    mounted(){
        this.load_data()
        // this.shield = this.data.shield
    },

    methods: {

        load_data(){
            this.$_send_({cmd:"item_get_shield", item_id:this.id}, response=>{
                this.shield = response.data
            })
        },

        ...mapActions([
            'confirm'
        ]),
        png_create: function(){
            // if(this.new_shildik.number1 == '' || this.new_shildik.number1 == null) return app.alert('У изделия отстутствует шильдик', 'is-warning')
            axios
                .get(this.url, {params: {cmd: 'create_png', item_id: this.id, t: new Date(),},responseType: 'blob'})
                .then((response) =>{
                    const url = window.URL.createObjectURL(new Blob([response.data], {type: 'application/pdf'}));
                    my_f.file_operation('load', url)
                })
        },

        add_shieldik_confirm: function(){
            this.confirm({
                text: 'Внимание! После присвоения номера по шильдику нельзя менять количество изделий и сам номер. Продолжить?',
                confirm_action:()=>{this.add_shildik()}
            })
        },
        

        add_shildik: function(){
            axios  
                .get(this.url, {params: {cmd: 'get_shildik_number', t: new Date()}})
                    .then((response) =>{
                        console.log(response.data)
                        let full_time = new Date()
                        this.shield.number1 = response.data + 1
                        this.shield.number2 = this.shield.number1 + (this.shield.amount - 1)
                        this.shield.year = full_time.getFullYear()
                        this.shield.month = full_time.getMonth() + 1
                    })
            
        },

        save_shield: function(){
            console.log(this.shield)
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'constructor_add_shield')
            bodyFormData.append('shield', JSON.stringify(this.shield))
            bodyFormData.append('item_id', this.id)
            axios({
                method: 'post',
                url: this.url,
                data: bodyFormData,
                headers: {'Content-Type': 'multipart/form-data' }
            })
            .then((response) =>{
                console.log(response.data)
                if(response.data.result){
                    this.data.isactive = false
                    this.$alert('Шильдик добавлен', 'is-success')
                }else {
                    this.$alert('Не удалось добавить шильдик', 'is-warning')
                }
                
            })
        }
    }
})
</script>

<style scoped>
/* .modal_back{
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgba(10,10,10,.86);
    z-index: 30;
} */

.shield_card{
    width: 500px;
    /* height: 50%; */
    background-color: rgb(236, 236, 236);
    padding: 10px;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
}

.input_s{
    outline: none;
    height: 30px;
    width: 50%;
    box-shadow: inset 0 0.0625em 0.125em rgb(10 10 10 / 5%);
    border: 1px solid lightgray;
}

.shield_num{
    display: flex;
    align-items: center;
    width: 50%;
}

.bottoms_buttons{
    display: flex;
    justify-content: flex-end;
    margin-right: 5px;
    flex-grow: 1;
    align-items: flex-end;
    flex-grow: 1;
}

.bottoms_buttons>*:first-child{
    flex-grow: 1;
}

.selector_row{
    display: flex;
    margin: 5px;
    align-items: center;
    justify-content: space-between;
    min-height: 30px;
}
</style>