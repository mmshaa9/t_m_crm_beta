<template>
    <div class="add_wrapper">
        <div class="add_block">
            <div class="modal-paragraph-header">Название изделия</div>
            <input type="text" v-model="item.name" placeholder="Название">
            <div class="modal-paragraph-header">Количество</div>
            <input type="text" v-model="item.count" placeholder="Количество">
            <div class="modal-paragraph-header">Дата отгрузки</div>
            <input placeholder="Введите дату в формате дд.мм.гггг" type="date" v-model="date">
            <div class="modal-paragraph-header">Файлы</div>                
            <div class="attachments_see">
                <div v-for="(file, index) in files" :key="index">
                    <div>{{file.name}}</div>
                    <i class="fas fa-trash-alt"  @click="files.splice(index,1)"></i>
                </div>    
            </div>
            <AppFileSelector class="btn" title="Загрузить изображение" :data="{multiple:true, accept:'.xls, .rar'}" @change="upload_and_apply_files($event)"> <i class="fas fa-upload"></i></AppFileSelector>                
            <button @click="save">Сохранить</button>
            <button @click="data.isactive = false">Отмена</button>
        </div>
        <b-loading :is-full-page="false" v-model="loading" :can-cancel="false"></b-loading>
    </div>
</template>



<script>
    import AppFileSelector from "@/components/app_file_selector.vue"
    import axios from "axios"
    import * as my from "@/my"
    export default ({
        props:['data'],
        components: {
            AppFileSelector
        },
        data: function(){
            return{
                files:[],
                item:{
                    deadline: null,
                    name:null,
                    count:null
                },
                alert:{
                    text: null,
                    visible: false
                },
                date: null,
                url: this.$store.getters.api_url_new,
                loading: false,
                user: this.$store.getters.USER_ID
            }

        },

        computed:{
            // close: function(){
            //     return this.add.visible
            // }
        },

        watch:{
            // close: function(){
            //     if(!this.add.visible){
            //         this.item.deadline = null,
            //         this.item.name = null,
            //         this.item.count = null,
            //         this.date = null
            //         this.files = []
            //     }
            // }
        },

        mounted: function() {

        },
        methods: {
            upload_and_apply_files: function(files) {                          
                my.mergeFile(this.files, files)             
            },

            save: function(){
                if(this.item.name == null || this.item.name == '') return this.$alert("Заполните поле 'Название'", 'is-warning')
                if(this.item.count == null || this.item.count == '' || this.item.count == 0) return this.$alert("Заполните поле 'Количество'", 'is-warning')
                if(this.date == null || this.date == '') return this.$alert("Заполните поле 'Дата отгрузки'", 'is-warning')
                if(this.files.length == 0 || this.files.length < 2) return this.$alert("Добавьте файлы чертежей и SW выгрузки", 'is-warning')
                for (let i = 0; i < this.files.length; i++) {
                    if(!this.check_name(this.files[i]['name'])){
                        return this.$alert("Добавьте файлы чертежей и SW выгрузки с правильными именами", 'is-warning')
                    }
                    
                }
                
                this.item.deadline = this.date
                this.item.name = my.org_name_normalization(this.item.name)
                this.loading = true
                var bodyFormData = new FormData()
                bodyFormData.append('cmd', 'production_add_item')
                for (let i = 0; i < this.files.length; i++){
                    bodyFormData.append("file["+i+"]", this.files[i])
                }
                bodyFormData.append('item', JSON.stringify(this.item))
                bodyFormData.append('user', this.user)
                axios({
                        method: 'post',
                        url: this.url,
                        data: bodyFormData,
                        headers: {'Content-Type': 'multipart/form-data' },
                        }) 
                .then(this.response)
            },

            response: function(response){
                this.loading = false
                if(response.data.result == "-ok"){
                    this.add.visible = false
                    this.$alert("Изделие успешно добавлено", 'is-success')
                    this.item = {name:null, count:null, deadline:null}
                    this.date = null
                    this.files = []
                    this.$emit('update')
                }else{
                    this.$alert("Не удалось добавить изделие, попробуйте позже", 'is-warning')
                }
            },

            check_name: function(name){
                if(name == "Все чертежи.rar" || name == "SW_Выгрузка.xls"){
                    return true
                }else{
                    return false
                }
            }
        } 
    })
</script>

<style scoped>

.add_block{
    padding: 10px;
    text-align: center;
    background: whitesmoke;
    display: flex;
    flex-direction: column;
    height: 100%;
}

.add_block>*{
    margin-top: 5px;
    height: 30px;
}

.add_wrapper{
    width: 40vw;
    height: 50vh;
}

.main_area{
    margin-bottom: 0px;
    padding: 0px;
    
}

.btn_add{
    border: 1px solid gray;
    padding: 5px;
}

.attachments_see{
    display: flex;
    flex-direction: column;
    height: 60px;
}

.attachments_see>*{
    display: flex;
    align-items: center;
    margin: 0px;
    padding: 5px;
}

.date_pick{
    margin: 0px 5px 0px 5px;
}

.input{
    border: 1px solid gray;
    box-shadow: 0px;
    border-radius: 0px;
    height: 36px;
}

.fa-trash-alt{
    padding-left: 5px;
}
</style>
