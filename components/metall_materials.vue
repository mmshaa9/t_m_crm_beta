<template>
    <div class="materials_area">
        <div class="materials_row bold materials_header">
            <div>Материал</div>
            <div>Цветной</div>
            <div>Нержавейка</div>
        </div>
        <div class="materials_wrapper">
            <div class="materials_row materials_item_row" v-for="material in materials" :class="{new_item: material.new == 'new'}" :key="material.Код" :ref="material.new">
                <div>
                    <i @click="delete_material(material)" class="fas fa-times-circle icons_hover" title="Удалить материал"> </i> 
                    <input v-model="material.Материал" @change="edit_material(material)" class="tm_input no_border">
                </div>
                <input type="checkbox" @change="set_material(material)" v-model="material.Цветной">
                <input type="checkbox" @change="set_material(material)" v-model="material.Нержавейка">
            </div>
        </div>
        <button class="button" @click="add_new">
Добавить новый материал
</button>
        <b-loading :is-full-page="false" v-model="isLoading" :can-cancel="false"></b-loading>
    </div>
</template>

<script>
import axios from 'axios'
import * as my_f from "@/my"
export default  {
    data(){
        return{
            materials:[],
            url: this.$store.getters.api_url_new,
            isLoading: false,
        }
    },

    computed:{
        
    },

    mounted(){
        this.load_data()
    },

    methods:{
        load_data(){
            this.isLoading = true
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'metall_get_forms_and_materials')
            axios({
                    method: 'post',
                    url: this.url,
                    data: bodyFormData,
                    headers: {'Content-Type': 'multipart/form-data' },
                    }) 
            .then((response)=>{
                // response.data.materials.forEach(el => {
                //     if(el.Цветной == 0){
                //         el.Цветной = false
                //     }else{
                //         el.Цветной = true
                //     }
                //     if(el.Нержавейка == 0){
                //         el.Нержавейка = false
                //     }else{
                //         el.Нержавейка = true
                //     }
                // });
                this.materials = this.get_materials_props(response.data.materials)
                this.isLoading = false
            })
        },

        add_new(){
            this.materials.push({Материал: null, Цветной:false, Нержавейка: false, new: 'new'})
            setTimeout(() => {
                this.$refs.new[0].scrollIntoView({required: false, behavior: 'smooth'})
            }, 100);
        },

        get_materials_props(materials){
            materials.forEach(el => {
                if(el.Цветной == 0){
                    el.Цветной = false
                }else{
                    el.Цветной = true
                }
                if(el.Нержавейка == 0){
                    el.Нержавейка = false
                }else{
                    el.Нержавейка = true
                }
            });

            return materials
        },

        edit_material(par){
            if(par.new == 'new'){
                this.add_new_material(par)
            }else{
                this.set_material(par)
            }
        },

        add_new_material(par){
            this.isLoading = true
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'metall_materials_add_new')
            bodyFormData.append('material', JSON.stringify(par))
            axios({
                    method: 'post',
                    url: this.url,
                    data: bodyFormData,
                    headers: {'Content-Type': 'multipart/form-data' },
                    }) 
            .then((response)=>{
                console.log(response.data)
                this.isLoading = false
                if(response.data.result == "-ok"){
                    app.alert('Операция успешно выполнена', 'is-success')
                    this.materials = this.get_materials_props(response.data.materials)
                }else{
                    app.alert('Не удалось выполнить операцию, попробуйте позже')
                }
            })
        },

        set_material(par){
            this.isLoading = true
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'metall_materials_change_prop')
            bodyFormData.append('material', JSON.stringify(par))
            axios({
                    method: 'post',
                    url: this.url,
                    data: bodyFormData,
                    headers: {'Content-Type': 'multipart/form-data' },
                    }) 
            .then((response)=>{
                console.log(response.data)
                this.isLoading = false
                if(response.data.result == "-ok"){
                    app.alert('Операция успешно выполнена', 'is-success')
                }else{
                    app.alert('Не удалось выполнить операцию, попробуйте позже')
                }
            })
        },

        delete_material(par){
            if(par.new == 'new'){
                this.materials.splice(this.materials.indexOf(par), 1)
                return
            }
            this.isLoading = true
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'metall_materials_delete')
            bodyFormData.append('id', par.Код)
            axios({
                    method: 'post',
                    url: this.url,
                    data: bodyFormData,
                    headers: {'Content-Type': 'multipart/form-data' },
                    }) 
            .then((response)=>{
                console.log(response.data)
                this.isLoading = false
                if(response.data.result == "-ok"){
                    app.alert('Операция успешно выполнена', 'is-success')
                    this.materials = this.get_materials_props(response.data.materials)
                }else{
                    app.alert('Не удалось выполнить операцию, попробуйте позже')
                }
            })
        }
    }
}
</script>

<style scoped>
    .materials_area{
        width: 700px;
        height: 500px;
        display: flex;
        flex-direction: column;
        overflow: hidden;
    }
    .materials_wrapper {
        display: flex;
        flex-direction: column;
        overflow: auto;
        padding: 5px;
        background: white;
    }

    .materials_header{
        padding: 0px 5px 0px 5px;
        border-bottom: 1px solid gray;
    }

    .materials_row {
        display: flex;
        align-items: center;
    }
    

    .materials_row>:first-child{
        flex-grow: 1;
    }

    .materials_row>*{
        width: 100px;
    }

    .materials_item_row{
        border-bottom: 1px solid lightgray;
        min-height: 30px;
    }

    .no_border{
        border: unset;
        height: 29px;
    }

    @keyframes mymove {
        50% {box-shadow: rgb(255 94 0 / 60%) 0px 2px 22px 2px;}
    }

    .new_item{
        animation: 2s mymove 1s 
    }

    .icons_hover{
        opacity: .7;
    }

    .icons_hover:hover{
        box-shadow: antiquewhite;
        text-shadow: 0px 0px 10px darkorange;
        cursor: pointer;
    }
</style>