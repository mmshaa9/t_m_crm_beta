<template>
    <div class="chat_app mat_des_shadow" :style="{width: data.w+'px', height:data.h+'px'}">
        <div  class="window">
            <b-button size='is-small' class="button_interface" @click="show_with_attach_value = true" >
Показать вложения
</b-button>
        </div>
        <div class="list_wrapper" ref="list_wrapper">
            <div id="list" class="list">
                <div  class="day" v-for ="day in message_list" :key="day.date">
                    <div class="date_title">
{{day.date}}
</div>	
                    <div v-for="(message, index) in day.messages" :message="message" :cur_user_id="cur_user_id" :key="index">
                        <div class="message_box"  :class="{ right: message.user_id == cur_user_id, left: message.user_id != cur_user_id}">
                            <div class="message_avatar" v-show="message.user_id != cur_user_id">
                                <div class="fas fa-user message_user"></div>	
                            </div>			
                            <div class="message_body">
                                <div class="message_window">
                                    <div v-if="message.user_id != cur_user_id" class="message_title">
{{message.user_name}}
</div>
                                    <div v-else class="message_title">
Я
</div>
                                    <div class="message_dan">
                                        <button  v-if="deletable(message)"    @click="deleteItself(message)" class="far fa-trash-alt message_delete" title="Удалить сообщение"></button>		
                                        <button @click="attach(message)" class="fas fa-paperclip button_attach_item" title="Прикрепить файл"></button>	
                                    </div>	
                                    
                                </div>
                                <div  class="message_text" v-html="wu(message.text)"></div>
                                <div  class="message_time">
{{message.time}}
</div>
                                <!-- <i class="fas fa-reply"></i>	 -->
                                <hr v-if="!!message.files2">
                                <div class="attach"  v-for="file in message.files2" :key="file.ФайлСсылка">
                                    <button id="btn_open_attach" class="attach_link" @click="open_attach(file)" title="Открыть вложение" :link="file.ФайлСсылка">
{{file.ФайлИмя + ' (' + file.ПользовательИмя + ', ' + file.ФайлДата + ' ' + file.ФайлВремя + ')'}}
</button>
                                    <button v-if="deletable2(file)"   class="far fa-trash-alt message_delete" @click="deattach(file)" title="Удалить вложение"></button>	
                                </div>	
                                    
                            </div>			
                        </div>
                    </div>
                </div>					
            </div>
        </div>

        <div class="controls2">				
            <textarea ref= "new_message_field" row="3" class="new_message" v-model="new_message"></textarea>
            <!-- <button class="button_global" v-on:click="postNewMessage">Отправить</button> -->
            <i @click="postNewMessage" title="Отправить" class="fas fa-location-arrow send_arrow"></i>

        </div>


        <div  v-show="show_with_attach_value" class="aa1">
            <div class="aa2">
                <div  v-for ="(day,index) in message_list" :day="day" :key="index">
                    <div v-for="(message, index) in day.messages" :message="message" :cur_user_id="cur_user_id" :key="index">
                        <div class="aa3"  v-for="file in message.files2" :key="file.ФайлСсылка">
                            <button id="btn_open_attach" class="aa4" @click="open_attach(file)" title="Открыть вложение" :link="file.ФайлСсылка">
{{file.ФайлИмя + ' (' + file.ПользовательИмя + ', ' + file.ФайлДата + ' ' + file.ФайлВремя + ')'}}
</button>
                        </div>	
                    </div>
                </div>										
                <button @click="show_with_attach_value = false" class="btn_hide_attachments button_global" >
Скрыть вложения
</button>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import * as my_f from "@/my"

export default ({

    props:['data'],

    data() {
        return{
            cur_conversation_id: '',
            cur_user_id:'',
            message_list:[],	
            new_message:'',
            close_chat: false,


            filter_text: '',
            refresh:false,
            first_load: true,

            show_with_attach_value: false,
            url: this.$store.getters.api_url_new,
            positions: {
                clientX: undefined,
                clientY: undefined,
                movementX: 0,
                movementY: 0
            },
            user:null
        }
    },

    computed:{
        watch_close(){
            return this.close_chat
        }
    },

    watch:{
        watch_close(){
            this.close_chat = true
        }
    },

    updated: function() {
        if (!this.first_load) return
        this.first_load =false
        // пролистываем до конца чат
        var end = this.$refs.list_wrapper;
        end.scrollTop = end.scrollHeight;            
    },

    beforeUpdated: function(){
        this.loadData()
    },


    mounted: function(){
        this.cur_conversation_id = this.data.id
        this.cur_user_id = this.data.user
        this.user = this.data.user
        this.loadData()
    },


    methods:{

        /*

            нужны функции:
            - добавить сообщение
            - удалить сообщение
            - прикрепить файл
            - удалить файл


        */

        show_with_attach: function(){
            this.show_with_attach_value = !this.show_with_attach_value
        },

        deleteMessage: function(id){
            //удаляем сообщение
            alert(id)
            return                

            axios
            .get(this.url, {params: {cmd: 'deleteMessage', message_id: m.id, user_id: this.cur_user_id, t: new Date()}})
            .then((response) =>{
                this.first_load = true                                        
                this.loadData()
            })
        },

        postNewMessage: function(){
            //отправляем на сервер новое сообщение
            this.$emit('post_message')
            if(this.cur_conversation_id == 0 || this.cur_conversation_id == '0' || this.cur_conversation_id == '') return app.alert('Сохраните заказ перед отправкой сообщений', 'is-warning')
            axios
            .get(this.url, {params: {cmd: 'postNewMessage', conversation_id: this.cur_conversation_id, user_id: this.user, text: this.new_message, t: new Date()}})
            .then((response) =>{
                this.first_load = true                                        
                this.loadData()
            })
                        
            console.log(this.new_message)
            this.new_message=''

        },

        loadData: function(){    			
            // загружаем заказы с сервера   
            if(this.cur_conversation_id == '' ||  this.cur_conversation_id == 0) return
            axios
            .get(this.url, {params: {cmd: 'getMessageList', conversation_id: this.cur_conversation_id, t: new Date()}})
            .then((response) =>{
                this.message_list = response.data
                // console.log(app.message_list)
            })
        },
        attach: function(m){
                //прикрепляем файл
                my_f.selectFile((files) =>{
                    // загружаем файлы по одному
                    for (var i = 0; i < files.length; i++) {
                        my_f.uploadFile({cmd: 'attachMessageAttachment', id: m.id, userfile: files[i], user: this.cur_user_id}, this.loadData, this.url)
                    }
                })
            },

            deattach: function (f) {
                // открепляем вложение
                axios
                .get(this.url, {params: {cmd: 'deattach', file_id: f.ФайлКод, user_id: app.cur_user_id, t: new Date()}})
                .then(function(response){                                        
                    app.loadData()
                })
            },

            open_attach: function(f){
                // открываем вложение

                console.log(f)
                // window.open(f.ФайлСсылка2)
                my_f.file_operation('open', f.link)
                // нужно добавить возможность открывать вложения в браузере
            },

            deleteItself: function(m){
                // удалить сообщение
                // нужен контроль прав удаления

                if(!confirm("Вы действительно хотите удалить сообщение?")) return
                
                axios
                .get(this.url, {params: {cmd: 'deleteMessage', message_id: m.id, user_id: app.cur_user_id, t: new Date()}})
                .then((response) =>{                                        
                    this.loadData()
                })
            },

            deletable: function(m){
                // удалять может только автор
                if(m.user_id != this.cur_user_id) return false
                // удалять можно сообщения, у которых нет просмотров
                // удалять можно сообщения, у которых нет дочерних элементов (задач и вложений)
                // лучше проверки этих условий проводить на сервере                
                return true
            },

            deletable2: function(m){
                
                if(m.ПользовательКод != this.cur_user_id) return false
                return true
            },

            wu: function(str){
                return my_f.wrapURL(str)
            },

            dragMouseDown: function (event) {
                event.preventDefault()
                // get the mouse cursor position at startup:
                this.positions.clientX = event.clientX
                this.positions.clientY = event.clientY
                document.onmousemove = this.elementDrag
                document.onmouseup = this.closeDragElement
            },
            elementDrag: function (event) {
                event.preventDefault()
                this.positions.movementX = this.positions.clientX - event.clientX
                this.positions.movementY = this.positions.clientY - event.clientY
                this.positions.clientX = event.clientX
                this.positions.clientY = event.clientY
                // set the element's new position:
                // this.$refs.draggableContainer.style.transform = 'translate3d(' + ((this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px,' + (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px, 0)')
                this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px'
                this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px'
            },
            closeDragElement () {
                document.onmouseup = null
                document.onmousemove = null
            }
    }
})
</script>

<style scoped>
.chat_app{		    
    display:flex;
    flex-direction: column;
    background: rgb(239, 239, 209);
    height: 100%;
    overflow: hidden;
    position: relative;
    border: 1px solid lightgray;
    transform-origin: right bottom;
    
}

.list_wrapper{
    margin:5px;
    border-radius: 5px;
    border:1px solid lightgray;
    flex-grow: 1;
    overflow: auto;
    background: white;
    max-height: 100%;
    min-height: calc(100% - 125px);
}


.list{
    display:flex;
    flex-direction: column;
    flex: 1 0 auto;

    /*
        ВНИМАНИЕ! 
        РЕШЕНИЕ ПРОБЛЕМЫ В IE
        ЧТОБЫ СОДЕРЖИМОЕ ПРАВИЛЬНО РАСТЯГИВАЛОСЬ ПО ВЕРТИКАЛИ:
        1. ДОБАВЛЯЕМ flex: 1 0 auto;
        2. САМ БЛОК С СОДЕРЖИМЫМ ПОМЕЩАЕМ В ДРУГОЙ БЛОК, В КОТОРОМ РЕАЛИЗУЕМ СКРОЛ
    */
}

.day{
    margin-bottom: 5px;	
}




.controls2{				
    height: 100px;
    flex-grow: 0;
    display: flex;
    align-content: flex-end;
    align-items: center;
}

.controls2>textarea{
    border: 1px solid lightgray;
    font-size:12pt;
    padding: 5px;
    margin:5px;
    border-radius: 5px;
}



.new_message{
    flex-grow: 1;
    resize: none;
}



.message_box{
    margin:5px;
    margin-right: auto;
    margin-left: auto;
    max-width: 70%;
    display: flex;
    flex-direction: row;

}

.message_avatar{
    margin:5px;
    padding:0px;				
    background: rgb(239,239,239);				
    width: 60px;
    height: 60px;
    min-width: 60px;
    border-radius: 30%;

    display:flex;
    justify-content: center;				
    align-items: center;

    border:1px solid lightgray;


    display: none;
}		

.message_body{
    margin:5px;
    padding:10px;				
    background: rgb(239,239,239);
    border-radius: 0 20px 20px 20px;
    border:1px solid lightgray;
}			

.message_title{				
    padding:0px;
    color: rgb(133, 145, 223);
    font-weight: bold;
    font-size:12pt;
}

.message_text{				
    padding:0px;
    margin: 0px;
    color: rgb(30, 30, 30);
    font-size:12pt;				
    line-height: 14pt;
}

.message_time{
    
    display: inline-block;
    font-size:10pt;
    color:rgb(150,150,150);
    width:auto;
    margin-left: auto;
    margin-right: 5px;				


}



    


.right{
    margin-right: 0px;
    justify-content: flex-end;
}



.left{
    margin-left: 0px;		
    justify-content: flex-start;
}



.right>.message_body{
    border-radius: 20px 0 20px 20px;	
}

.left>.message_body{
    border-radius: 0 20px 20px 20px;	
}



.date_title{
    background: none;
    color:gray;
    margin:auto;
    padding: 5px;
    
    text-align: center;
    font-weight: bold;
}

.empty_avatar{
    margin:0px;
    color:white;
    font-size:36pt;				
}




.controls{
    position: fixed;				
    top:  calc(100% - 65px);
    right: 5px;
    
}


.controls>button{
    border-radius: 50%;
    width: 50px;
    height: 50px;
    border:1px solid lightgray;
    margin:5px;
    padding: 2px;
    text-align: center;
    outline: none;
}

.controls>button:hover{
    background: orange;
}


hr{
    padding: 0px;
    margin-bottom: 0px;
}


.attach>button, .attach_new{
    border:none;
    outline:none;
    background: none;
    font-size: 8pt;
    cursor: pointer;
    padding: 0px;
    margin: 0px;
}

.attach_link:hover, .attach_new:hover{
    text-decoration: underline;
    color:blue;
}

.attach_del{
    /* color:grey; */
    /* display:none; */
}


.attach_del:hover{
    text-decoration: underline;
    color:red;
}


.attach:hover .attach_del{				
    display:inline-block;

}

.btn_show_attachments{
    border: 1px solid gray;
    background: none;
    border-radius: 2px;
    margin:5px;
    margin-bottom:0px;
    padding:5px;
}


.aa1{
    
    position:absolute;
    top:0;
    left:0;
    width:100%;
    height:100%;
    background:rgba(0,0,0,.5);
    display:flex;
    flex-direction:column;
}

.aa2{
    padding:5px;
    background:lightgray;
    width:75%;		
    max-width:300px;		
    margin:auto;
    border-radius:5px;
}

.aa3{
    
}

.aa4{
    margin:2px auto;
    width:100%;
    border: 1px solid gray;
    border-radius:5px;
}

.aa4:hover{
    color: blue;
    text-decoration:underline;

}
.window {
    padding: 5px;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
}
.btn_hide_attachments {
    width:100%;
    margin:5px 0px;
}
.message_user {
    margin:0px;
    color:white;
    font-size:36pt;
}
.message_window {
    display: flex;
}
.message_dan {
margin-right: 0px;
margin-left: auto;
padding-left: 15px;
}
.message_delete {
    background: none;
    border:none;
    font-size: 11pt;
    color:gray;
    margin:0px;
    padding: 0px;
    cursor: pointer;
}
.message_attach {
    background: none;
    border: none;
    font-size: 11pt;
    color:gray;
    margin:0px;
}

.fa-times {
    cursor: pointer;
}
.send_arrow{
    transform: rotate(45deg);
    width: 48px;
    height: 48px;
    display: flex;
    align-items: center;
    font-size: 35px;
    /* border: 1px solid lightgray; */
    color: rgb(252, 177, 38);
    border-radius: 50%;
    justify-content: center;
    align-content: center;
    margin-right: 10px;
}
</style>
