<template>
    <div class="defect_block lock">
        <!-- окно со списком пометок по изделию -->
        <button v-if="data.item.defect_count > 0" v-on:click="production_show_comments(data.item)" class="open_modal_button">Показать замечания ({{data.item.defect_count}})</button>
        <div v-if="modal.comment_list.visible" class="modal-wrapper">
            <div class="modal-block new_task_modal lock">
                <span class="modal_block_header">Замечания к изделию ID {{data.item.id}}</span>
                <div class="insblock_content">
                    <div class="comment_area" v-for="comment in data.item.comments" v-bind:key="comment.id">
                        <div class="comment_row">
                            <span class="bold created">{{comment.создание + ' ' + '(' + comment.Сотрудник + ')' + ':'}}</span>
                            {{comment.примечание}}
                        </div>
                        <my-attachments v-bind:data="{empty: comment.Количество_файлов == 0 , items: comment.defect_attachments || []}" @load-attachments = "load_attachments(comment, data.item)" @hide-attachments = "hide_attachments(comment)" @open-attachment = "open_attachment($event, comment)" @add-attachment="add_attachment_to_comment(comment, data.item)" @del-attachment = "delete_attachment_on_comment($event, comment)"></my-attachments>
                    </div>
                </div>
                <button class="button button-cancel button_global" v-on:click="hide_comments">Назад</button>

                <my-modal-alert v-bind:data="modal.alert"></my-modal-alert>
                
            </div>
        </div>
        <!-- окно для создания пометки для изделия -->
        <button  v-on:click="show_create_comment" class="open_modal_button">Добавить замечание</button>
        
        <div v-if="modal.create_comment.visible" class="modal-wrapper">
            <div class="modal-block new_task_modal">
                <span class="modal_block_header">Добавить замечание к изделию ID {{data.item.id}}</span>
                <textarea rows="10" cols="45" v-model="modal.create_comment.text"></textarea>
                <div v-on:click="production_defect_add_attachment(data.item)" class="attach_file_modal">
                    <i class="fas fa-paperclip button_attach_item"></i>
                    <button class="button_sheet button_global">Прикрепить файл</button>	
                </div>
                <div v-if="data.item.list_files != 0" class="attached_files">
                    <div v-for="(list_file, index) in data.item.list_files" class="file_row" v-bind:key="index">
                        <div class="file_title">{{list_file.name}}</div>
                        <i v-on:click="production_defect_deattach(data.item, index)" title="Удалить вложение" class="fas fa-trash-alt button_attach_item"></i>
                    </div>								
                </div>							
                <button v-on:click="production_add_defect_comment(data.item)" class="button_interface">Сохранить пометку</button>
                
                

                <button class="button button-cancel" v-on:click="hide_create(data.item)">Отмена</button>
            </div>
        </div>
        <my-modal-alert v-bind:data="modal.alert"></my-modal-alert>    
    </div>
</template>
<script>
/* КОМПОНЕНТ ПОЗВОЛЯЕТ ДОБАВИТЬ И ПРОСМОТРЕТЬ ЗАМЕЧАНИЯ ПО ИЗДЕЛИЮ
    НЕОБХОДИМО ИМЕТЬ ДАННЫЕ ПО НАЛИЧИЮ ЗАМЕТОК ДЛЯ ОТОБРАЖЕНИЯ В КОМПОНЕНТЕ ВЛОЖЕНИЙ НАЛИЧИЕ ФАЙЛОВ
    данные передаются через data со структурой: 


        data: {
            item: {
                defect_count: 0,
                comments: {
                    defect_attachments: {
                        object:{
                                name2: 'name',
                                name: 'name+user+date_created'
                        }
                    }
                }
            }
        }




*/


    module.exports = {
        props:['data'],
        data: function() {
            return {  
                modal: {
                    create_comment:{ visible: false, text: ''},
                    comment_list:{ visible: false},
                    alert: { visible: false, text: ''}
                },
            }
        },

        components: {
            'my-modal-alert': httpVueLoader('/crm/src/components/common_old/my-modal-alert.vue?t=' + new Date()),
            'my-attachments': httpVueLoader('/crm/src/components/common_old/my-attachments.vue?t=' + new Date()),
        },

        computed: {
            
        },
        
        methods:{

            // ФУНКЦИИ ПО ДОБАВЛЕНИЮ ПОМЕТОК К ИЗДЕЛИЯМ///////////////////////////////////////////////////////////////////////////////////////

            // общая функция выбора файлов для вложений
            // форматирует объекты Filelist в массив
            // сравнивает имена файлов для создания неповторяющихся элементов списка файлов
            // отдает новый список в коллбэке
            select_file_to_attach: function(cur_files, callback){
                // var show_alert = this.show_alert
                var tmp
                selectFile(function(files){            
                    // выбираем прикрепляемые файлы                

                    rebuilded_files = []

                    rebuildFile(files, rebuilded_files)
                    
                    if(typeof cur_files != 'object'){
                        cur_files = []                    
                    }                

                    tmp = mergeFile(cur_files, rebuilded_files)                                    
                    
                    callback(tmp)
                })                
            },
            // функции по окну создания пометки

            production_add_defect_comment: function(item){
                //отправляем текст пометки и файлы на сервер
                var hide_create = this.hide_create
                var show_alert = this.show_alert
                axios
                    .get('danila/defect.php', { params: { cmd: 'production_add_comment', item_id: item.id, order_id: item.order_id, text: this.modal.create_comment.text, cu: cur_user, t: new Date()}})
                    .then(function (response) {
                        // передаем в коллбэке код комментария
                        // чтобы присвоить коду файла код заметки
                        
                        if(response.data.result == '-ok'){
                            // присваиваем id комментария, что бы передать его 
                            // обновляем количество заметок
                            item.comment_id = response.data.id
                            item.defect_count = response.data.defect_count
                            var data = { cmd: 'production_defect_add_attachment', id: item.comment_id}                    
                            if(item.list_files){
                                for (var i = 0; i < item.list_files.length; i++) {                            
                                    uploadFile({cmd: data.cmd, id: data.id, userfile: item.list_files[i], user: this.cur_user}, function(){
                                        // production_showAttachment(item)
                                        
                                    },
                                    "crm/backend/index.php")
                                }
                            }
                            show_alert('Замечание добавлено')
                            hide_create(item)
                        }else{
                            show_alert('Не удалось добавить замечание. Попробуйте снова.')
                        }

                    }) 
                    .catch(function (data) {
                        // Ловит ошибки в axios
                        console.log(data)
                        show_alert('Не удалось добавить замечание.')
                        return true
                    })
            },

            production_defect_add_attachment: function(item){
                // добавляем файлы к пометке
                // формирует массив с файлами без загрузки на сервер
                var show_multiple_file_warning = this.show_multiple_file_warning
                this.select_file_to_attach(item.list_files, function(tmp){                  
                    item.list_files = tmp.res_correct
                    console.log(tmp)
                    show_multiple_file_warning(tmp)
                })
            },

            show_multiple_file_warning: function (data){
                // функция для отображения алерта по повторяющимся файлам
                var show_alert = this.show_alert
                if(data.res_repeated.length>0){    
                    var r = 'Следующие файлы уже добавлены:<br> '                
                    data.res_repeated.forEach(function(e) {
                        r = r + ' - <b>' + e.name + '</b><br>' 
                    })
                    show_alert(r)
                }
            },

            production_defect_deattach: function(item, index){
                // открепить выбранные вложения от заметки
                // удаляет из созданного массива с файлами элемент
                var list_files = item.list_files
                list_files.splice(index, 1)
            },

            show_create_comment: function(){
                // функция показа модального окна создания пометки
                // 
                this.modal.create_comment.visible = true;
            },

            hide_create: function(item){
                // функция скрытия модального окна создания заметки
                // обнуляет массив с файлами и набранный текст
                this.modal.create_comment.visible = false;
                item.list_files = ""
                item.list_files_new = ""
                this.modal.create_comment.text = ''
            },  



            // функции по списку заметок и вложений к ним /////////////////
            
            add_attachment_to_comment: function(comment){
                // добавляем вложение к существующей пометке
                // функция возвращает обновленный список вложений в пометке
                var show_alert = this.show_alert
                var load_attachments = this.load_attachments
                var select_file_to_attach = this.select_file_to_attach
                var show_multiple_file_warning = this.show_multiple_file_warning
                //получаем список ранее добавленных заметок в комментарии
                axios
                    .get('danila/defect.php', { params: { cmd: 'production_get_attachment_list', comment_id: comment.Код, t: new Date() }})
                    .then( function(response){
                        comment.defect_attachments_new = response.data
                        // выбираем файлы универсальным селектором
                        // для проверки на повторяющиеся файлы
                        select_file_to_attach(comment.defect_attachments_new, function(tmp){
                            
                            comment.defect_attachments_new = tmp.res_correct
                            
                            for(var i = 0; i < tmp.res_correct.length; i++){
                                var data = { cmd: 'production_defect_add_attachment', id: comment.Код, userfile:tmp.res_correct, user: this.cur_user }
                                uploadFile({ cmd: data.cmd, id: data.id, userfile: data.userfile[i], user: data.user }, function () {
                                    load_attachments(comment)
                                    },'crm/backend/index.php')
                                }
                            if(tmp.res_repeated.length > 0){
                                show_multiple_file_warning(tmp)
                            }else {
                                comment.Количество_файлов = tmp.res_correct.length
                                show_alert('Файл(ы) добавлен(ы)')
                            }                            
                        })
                    })
            },
            
            production_show_comments: function(item){
                //получаем список всех заметок для конкретного изделия
                var show_comments = this.show_comments
                axios
                    .get('danila/defect.php', { params: { cmd: 'production_get_comments_list', item_id: item.id, t: new Date()}} )
                    .then( function(response){
                        item.comments = response.data 
                        console.log(item.comments)
                        console.log(response.data) 
                        show_comments()                 
                    })
                
            },

            show_comments: function() {
                //функция для показа модального окна со списком пометок
                this.modal.comment_list.visible = true

            },
                    
            hide_comments: function(){
                // функция для скрытия модального окна со списком пометок
                this.modal.comment_list.visible = false;
            },
   
            load_attachments: function(comment){
                // загружаем вложения для конкретной пометки      
                axios
                .get('danila/defect.php', { params: { cmd: 'production_get_attachment_list', comment_id: comment.Код, t: new Date() }})
                .then( function(response){
                    comment.defect_attachments = response.data
                })
            },
            
            hide_attachments: function(comment){
                //скрываем вложения
                console.log('hide')
                comment.defect_attachments = []
            },
            
            open_attachment: function(defect_attachment, comment){
                //открываем вложение и скачиваем
                console.log('open')

                if (viewmode == 0){
                    // отрабатываем событие в браузере
                    console.log(defect_attachment.url2)
                    downloadFile(defect_attachment.url2)
                }else{
                    ms_access_click3()
                }
            },

            delete_attachment_on_comment: function(id, comment){
                // удалить вложение в пометке
                console.log('del')
                console.log(id)
                if(this.deletable(comment)){    
                    axios
                    .get('danila/defect.php', {params: {cmd: 'production_delete_attach_from_comment', attach_id: id, comment_id: comment.Код}})
                    .then(function(response){
                        console.log(response.data)
                        console.log()
                        comment.defect_attachments = response.data
                        if (comment.defect_attachments == 0 ){
                            comment.Количество_файлов = 0
                        }
                    })
                } else{
                    this.show_alert('Недостаточно прав для удаления файла!')
                }
            },

            deletable: function(comment){
                // проверка, можно ли удалять вложение
                // удалять может только автор
                if(comment.Сотрудник_код != this.cur_user) return false               
                return true
            },
            
            // функции для вывода сообщений в компоненте my-modal-alert

            show_alert: function(text){
                // выводим сообщение
                this.modal.alert.text = text
                this.modal.alert.visible = true            
            },

            hide_alert: function(){   
                // скрываем сообщение
                this.modal.alert.visible = false
            },
        }

    }
</script>

<style scoped>

.defect_block {
    display:inline;
    margin: 0;
    padding: 0;
}

.modal_block_style {
  margin: 5px;
  border: 1px solid gray;
  background: white;
  text-align: left;
  max-height: 300px;
  overflow: auto;
}

.modal_block_header {
    font-weight: bold;
    text-decoration: underline;
}

.insblock_content {
  padding: 5;
  min-height: 300px;
  max-height: 300px;
  overflow: auto;
  border: 1px solid gray;
  background: white;
}

.modal-wrapper {
  margin: 0;
  padding: 0;
}

.comment_row {
  /* display: flex; */
  margin: 0;
  padding: 0;
  text-align: left;
  text-transform: none; 
  word-break: break-word;
}

.comment_area {
  margin: 0;
  padding: 0;
}

.created {
  margin: 0;
  padding: 0;
  white-space: nowrap;
  text-transform: uppercase;
}

.comment_text {
  margin: 0;
  padding: 0;
  text-transform: none;
}

.button_sheet_grey {
  border: none;
  outline: none;
  text-decoration: underline;
  padding: 0px;
  margin: 0px;
  margin-right: 5px;
  font-size: 10pt;
  text-transform: none;
}

.attached_files {
  font-size: 10pt;
    max-height: 200px;
    margin: 5px;
    padding: 0;
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    text-transform: initial;
}

.file_row {
  display: flex;
  margin: 0;
  margin-bottom: 5px;
  padding: 0;
  min-height: 20px;
  margin-left: 5px;
  align-items: center;
  /* justify-content: space-between; */
}

.file_row > * {
  margin: 0;
  padding: 0;
}

.fas {
  margin-left: 10px
}

.attachments-block {
  margin: 0;
  text-align: left;
  margin-bottom: 15px;
  padding-top: 0px;
}


.attachments-items {
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
}


.attachments-block>*>* {
  margin: 0;
  padding: 0;
  text-align: left;
}

.attachments-block>*>*>* {
  margin: 0;
  padding: 0;
  margin-top: 5px;
  font-size: 8pt;
}

.attachments-block>*>*>*:hover {
  color: blue;
}

.btn {
  border: none;
  outline: none;
  text-decoration: underline;
  padding: 0px;
  margin: 0px;
  margin-right: 5px;
  font-size: 8pt;
  text-transform: none;
  cursor: pointer;
}

.open_modal_button {
    border: none;
    outline: none;
    text-decoration: underline;
    padding: 0px;
    margin: 0px;
    margin-right: 5px;
    font-size: 10pt;
    text-transform: none;
    cursor: pointer;
    background-color: unset;
}

.open_modal_button:hover {
    color:blue;
}

.attach_file_modal {
  margin: 0;
  padding: 0;
  text-align: left;
}

.modal-wrapper {
  color: black;
}

.block {
  color: black;
}

.button_sheet:hover {
  color: gray;
}

.fas {
  cursor: pointer;
}

.button_attach_item {
  font-size: 10pt;
}

.lock_scroll {
    overflow: hidden !important;
}
</style>





















