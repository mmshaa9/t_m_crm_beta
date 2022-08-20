<template>
    <div class="modal_card">
        <div>
            <label class="field_label" for="name">Наименование</label>
            <b-input if="name" type="textarea" v-model="data.new_item.name"  rows="6"></b-input>
        </div>
        <div> 
            <label for="order"  class="field_label">Заказ</label>
            <b-input if="order" v-model="data.new_item.order_id" expanded size='is-small' custom-class="gray_readonly" readonly type="text"></b-input>
        </div>
        <div> 
            <label class="field_label">Количество</label>
            <b-input v-model="data.new_item.count" type="number" expanded size='is-small'></b-input>
        </div>
        <div> 
            <label for="date" class="field_label">Отгрузка</label>
            <b-input  v-model="data.new_item.deadline" v-if="data.new_item.order_id == null"  expanded size='is-small' type="date"></b-input>
            <b-input  v-model="data.new_item.deadline" v-else custom-class="gray_readonly" readonly expanded size='is-small' type="date"></b-input>
        </div>
        <div class="bot_but">
            <b-button size="is-small" @click="data.new_item.save_data">
Ок
</b-button>
            <b-button size="is-small" @click="data.isactive = false" class="cancel_button">
Отмена
</b-button>
        </div>
    </div>
</template>

<script>
    module.exports = ({
        props: ['data'],
        data: function(){
            return {

            }
        },

        methods: {
            
        }
    })
</script>

<style scoped>
.modal_card {
    background-color: #ececec ;
    padding: 10px;
    border-radius: 10px;
    width: 500px;
}

.bot_but{
    display: flex;
    justify-content: flex-end;
    margin-top: 5px;
}
</style>