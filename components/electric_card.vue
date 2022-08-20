<template>
<div class="content_elec">
    <div class="modal_area_elec">
        <chat :convs_id="data.item.convers_id" :user="cu" :key="data.item.chat_key"></chat>
        <div class="attached_files ">
            <div class="header_files">
                <div>Файлы</div>
                <div class="attachments_down">
                    <my-attachments v-bind:user="data.item.user" v-bind:data="{ empty: true, items: data.files.files || []}" @open-attachment ="data.files.open_attachment($event)"   @add-attachment ="data.files.add_attachment(data.attach)" @del-attachment ="data.files.del_attachment($event, data.files, data.item)"></my-attachments>
                    <select-box :data="data.attach" @select_item ="data.attach.select_file_type($event, data.attach)" @confirm_action ="data.attach.load_attachment($event, data.attach, data.item, data.files)"></select-box>
                </div>
            </div>
        </div>
    </div>
    <b-button @click="data.item.elec_bill(data.item)" size="is-large">Выгрузить ведомость покупных и материалов</b-button>
    <div class="bottom_buttons_item">
        <b-button size="is-medium" @click="data.item.add_open_box(data.item, data.files)">Прикрепить фото ОТКРЫТОГО щита</b-button>
        <b-button size="is-medium" @click="data.item.add_close_box(data.item, data.files)">Прикрепить фото ЗАКРЫТОГО щита</b-button>
        <b-button size="is-medium" @click="data.item.pack_box(data.item, data.files)" class="cancel_button">Прикрепить фото УПАКОВАННОГО щита</b-button>
        <b-button size="is-medium" @click="data.item.signed_bill(data.item, data.files)" class="cancel_button">Прикрепить скан ЗАПОЛНЕННОЙ ведомости</b-button>
    </div>
</div>
</template>

<script>
module.exports = ({
        props:['data'],
        data: function(){
            return {
                positions: {
                    clientX: undefined,
                    clientY: undefined,
                    movementX: -0,
                    movementY: -0,
                    active: false              
                },
                cu: app.$store.getters.USER_ID,
                full_width: false
            }
        },

        components:{
            'chat': main_chat,
            'my-attachments': my_attachments,
            'select-box': select_box
        },
        updated(){
        },

        methods:{
        }
    })
</script>

<style scoped>

.content_elec{
   display: flex;
    flex-direction: column;
    height: 660px;
    width: 1000px;
    overflow: auto;
}
.chat_app{
    width: 50%;
    border: 1px solid lightgray;
}

.modal_area_elec{
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
  padding: 10px;
  background: whitesmoke;
  border-bottom: 1px solid lightgray;
  height: 70%;
}

.bottom_buttons_item{
    display: flex;
    justify-content: flex-end;
    padding-right: 5px;
    height: 18%;
    justify-content: space-between;
}

.button {
  border: 1px solid gray;
  white-space:unset;
  height: unset;
}

.attached_files {
    display: flex;
    border: 1px solid lightgray;
    padding: 5px;
    background-color: white;
    height: 100%;
    overflow: auto;
    width: 50%;
    margin-left: 5px;
}

.btn {
    text-decoration: unset !important;
    background-color: #ffe08a;
    border-radius: 2px;
    border-color: #b9b9b9;
    border-width: 1px;
    color: #363636;
    cursor: pointer;
    justify-content: center;
    padding-bottom: calc(.5em - 1px);
    padding-left: 1em;
    padding-right: 1em;
    padding-top: calc(.5em - 1px);
    text-align: center;
    white-space: nowrap;
    transition: all 0.25s ease-in-out 0s;
}

.btn:hover{
    background-color: #fdd873;
    border-color: transparent;
    color: #3a3939;
}

.hide_attachment_par {
    display: none;
}

.button{
    position: unset
}


</style>