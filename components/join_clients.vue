<template>
    <div class="modal_area">
        <input  placeholder="Поиск клиентов" v-model="data.filter_text" @input="data.filter" class="select_box_input" type="search">
        <div class="item_list">
            <div v-for="(client, index) in data.clients" :key="index" class="table_row_new">
                <div class="checkbox_container">
                    <input class="checkbox" type="checkbox" @change="unite(client)" v-model="client.join" placeholder="Выбрать для объеденения">
                </div>
                <div>
                    <div>Клиент: 
                        <span class="bold">{{client.Название}}</span>
                        <span v-if="client.оригинальный_клиент == 1" class="original_client">Оригинальный клиент</span>
                        <span v-else class="original_client duplicate">Дубль</span>
                    </div>
                    <div>Адрес: <span class="bold">{{client.Адрес}}</span></div>
                    <div>Контакт: <span class="bold">{{client.Контакт}}</span></div>
                </div>
                
            </div>
        </div>
        <div class="bottom_buttons">
            <b-button size="is-small" @click="data.clients_join">
Объеденить
</b-button>
            <b-button size="is-small" class="cancel_button" @click="data.isactive = false">
Отмена
</b-button>
        </div>
    </div>
</template>

<script>
export default ({

    props:['data'],
    // data(){
    //     return{
    //         isactive: false
    //     }
    // },

    computed:{
        default_(){
            return this.data.isactive
        }
    },

    watch:{
        default_(new_, old){
            console.log(new_, old)
            if(!new_){
                this.data.filter_text = ''
                this.data.clients = []
                this.data.united_clients = []
            }
        }
    },

    mounted(){
        console.log(this.data)
        // this.isactive = this.data.isactive
    },

    methods:{
        unite(par){
            if(par.join){
                this.data.united_clients.push(par)
            }else{
                this.data.united_clients.splice(this.data.united_clients.indexOf(par), 1)
            }
        }
    }
})
</script>

<style scoped>

    .modal_area{
        display: flex;
        flex-direction: column;
        padding: 10px;
        overflow: hidden;
        width: 800px;
        height: 560px;
    }

    .item_list {
        padding: 5px;
        flex-grow: 1;
        background: white;
        border: 1px solid gray;
        overflow: auto;
    }

    .select_box_input{
        margin-bottom: 5px;
        min-height: 30px;
        outline: none;
    }

    .table_row_new{
        overflow: hidden;
        padding: 5px;
        position: relative;
        display: flex;
    }

    .table_row_new:hover{
        background: rgb(255, 250, 242);
        cursor: pointer;
    }
    
    .bottom_buttons{
        display: flex;
        justify-content: flex-end;
    }

    .checkbox_{
        display: flex;
        justify-content: flex-end;
    }

    .checkbox_container{
        display: flex;
        flex-direction: column;
        justify-content: center;
        padding-right: 5px;
        border-right: 1px solid gray;
        margin-right: 10px
    }

    .checkbox{
        cursor: pointer;
        transform: scale(1.2);
    }

    .original_client{
        border: 1px solid lightgray;
        padding: 0px 4px 0px 4px;
        background: rgb(249 249 249);
        border-radius: 30px;
        font-size: 11px;
    }

    .duplicate{
        background: #f17a00a8;
    }
</style>