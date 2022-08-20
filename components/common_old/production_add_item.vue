<template>
    <div class="main_area">
        <button class="btn_add" @click="add.visible = !add.visible">Добавить в производство</button>
        <div class="modal-wrapper" v-if="add.visible">
            <div class="modal-block">
                <div class="modal-header">Новое изделие</div>
                <div class="modal-paragraph-header">Название изделия</div>
                <input type="text" v-model="item.name" placeholder="Название">
                <div class="modal-paragraph-header">Количество</div>
                <input type="text" v-model="item.count" placeholder="Количество">
                <div class="modal-paragraph-header">Дата отгрузки</div>
                <input placeholder="Введите дату в формате дд.мм.гггг" type="text" v-model="date">
                <div class="modal-paragraph-header">Файлы</div>                
                <div class="attachments_see">
                    <div v-for="(file, index) in files" :key="index">
                        <div>{{file.name}}</div>
                        <i class="fas fa-trash-alt"  @click="files.splice(index,1)"></i>
                    </div>    
                </div>
                <app-file-selector class="btn" title="Загрузить изображение" :data="{multiple:true, accept:'.xls, .rar'}" @change="upload_and_apply_files($event)"> <i class="fas fa-upload"></i></app-file-selector>                
                <button @click="save">Сохранить</button>
                <button @click="add.visible = false">Отмена</button>
            </div>
        </div>
        <my-alert v-bind:data="alert"></my-alert>
    </div>
</template>



<script>
    module.exports = ({
        props:['add'],
        components: {
            'app-file-selector': httpVueLoader('/crm/src/components/common_old/app_file_selector.vue?t=' + new Date()),
            'my-alert': httpVueLoader('/crm/src/components/common_old/my-modal-alert.vue?t=' + new Date()),
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
                url: "/crm/backend/index.php",
            }

        },

        computed:{
            close: function(){
                return this.add.visible
            }
        },

        watch:{
            close: function(){
                if(!this.add.visible){
                    this.item.deadline = null,
                    this.item.name = null,
                    this.item.count = null,
                    this.date = null
                    this.files = []
                }
            }
        },

        mounted: function() {

        },
        methods: {
            upload_and_apply_files: function(files) {                          
                mergeFile(this.files, files)             
            },

            save: function(){
                if(this.item.name == null || this.item.name == '') return this.alert = {visible:true, text: "Заполните поле 'Название'"}
                if(this.item.count == null || this.item.count == '' || this.item.count == 0) return this.alert = {visible:true, text: "Заполните поле 'Количество'"}
                if(this.date == null || this.date == '') return this.alert = {visible:true, text: "Заполните поле 'Дата отгрузки'"}
                if(this.files.length == 0 || this.files.length < 2) return this.alert = {visible:true, text: "Добавьте файлы чертежей и SW выгрузки"}
                for (let i = 0; i < this.files.length; i++) {
                    if(!this.check_name(this.files[i]['name'])){
                        return this.alert = {visible:true, text: "Добавьте файлы чертежей и SW выгрузки с правильными именами"}
                    }
                    
                }
                
                this.item.deadline = this.date
                this.item.name = org_name_normalization(this.item.name)
                this.add.indicator = true
                var bodyFormData = new FormData()
                bodyFormData.append('cmd', 'production_add_item')
                for (let i = 0; i < this.files.length; i++){
                    bodyFormData.append("file["+i+"]", this.files[i])
                }
                bodyFormData.append('item', JSON.stringify(this.item))
                bodyFormData.append('user', this.add.user)
                axios({
                        method: 'post',
                        url: this.url,
                        data: bodyFormData,
                        headers: {'Content-Type': 'multipart/form-data' },
                        }) 
                .then(this.response)
            },

            response: function(response){
                if(response.data.result == "-ok"){
                    this.add.indicator = false
                    this.add.orders = response.data.orders
                    this.add.visible = false
                    this.alert = {visible:true, text: "Изделие успешно добавлено"}
                    this.item = {name:null, count:null, deadline:null}
                    this.date = null
                    this.files = []
                }else{
                    this.alert = {visible:true, text: "Не удалось добавить изделие, попробуйте позже"}
                    this.add.indicator = "error"
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
