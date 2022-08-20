
<template>
    <div class="attachments-block">
        <button class="btn" style="color:blue;" v-if="!data.empty && data.items.length == 0" @click="$emit('load-attachments')">
Показать вложения
</button>                
        <button class="btn hide_attachment_par" v-if="data.items.length>0" @click="$emit('hide-attachments')">
Скрыть вложения
</button>                
        <button :disabled="is_add_attachemnt_button_disabled" :class="{disabled: is_add_attachemnt_button_disabled}" class="btn" @click="$emit('add-attachment')">
Прикрепить вложение
</button>
        <div class="attachments-items" v-if="data.items.length>0">
            <div v-for="(item , index) of data.items" :key="index">
                <button id="open-attachment" class="attachment-name" @click="$emit('open-attachment', item)" :link="item.url2">
{{(index+1) + '. ' + item.name}}
</button>
                <button v-if="data.deleteble ?? user == item.сотрудник " class="attachment-del far fa-trash-alt" @click="$emit('del-attachment', item.id)"></button>
            </div>
        </div>
        
    </div>
</template>
<script>
    /*
        ВАЖНО! САМ КОМПОНЕНТ НЕ ИМЕЕТ ФУНКЦИОНАЛА ПО ОПЕРИРОВАНИЮ С ДАННЫМИ
        ТОЛЬКО ОТОБРАЖЕНИЕ И ПЕРЕДАЧА КОМАНД РОДИТЕЛЮ
        ВЕСЬ ФУНКЦИОНАЛ РЕАЛИЗУЕТСЯ В РОДИТЕЛЕ

        компонент вложений
        отображает содержимое вложений и функционал по взаимодействию с ними

        все отображаемые данные принимаются от родителя через свойство "data" со следующей структурой        
        data:{
                empty:false,
                count:3,
                items:[
                    {id:0, name:'Накладная.pdf', url:''},
                    {id:1, name:'Договор.jpg', url:''},
                    {id:2, name:'Акты.jpg', url:''},
                ]
            }



        возможности:
        - показать-скрыть вложения
        - добавить
        - удалить        



    */
    export default {
        props:['data', 'user'],
        data: function() {
            return {                
              expand:false
            }
        },
        computed:{
            is_add_attachemnt_button_disabled(){
                return this.data.disabled ?? false
            }
        }      
    }
</script>


<style scoped>
    button{
        border:none;
        cursor: pointer;
    }

    .attachments-block{
        /*border:1px solid gray;
        margin:5px;
        
        */
        padding:5px;        
    }

    .attachments-block .btn{        
        text-decoration: underline;
    }
</style>