<template>
    <div class="modal_area">
        <input v-model="data.filter_text" v-if="data.filter" @input="data.filter" placeholder="Поиск" class="select_box_input" type="search">
        <div class="item_list">
        <div @click="data.select_item(item)" @dblclick="data.open_draw(item)" class="table_row_new" v-for="(item, index) in data.items" :key="index" :class="{choosen_par: data.active_item == item}">
            <div class="items_val">
                <span class="marg">Обозначение:</span><span class="bold marg">{{item.Обозначение}}</span>
                <span class="marg">Деталь:</span><span class="bold marg">{{item.Деталь}}</span>
            </div>
            <div class="items_val">
                <span class="marg">Изделие:</span><span class="bold props_val marg">{{item.Изделие}}</span> 
                <span class="marg">Конструктор:</span><span class="bold marg">{{item.Конструктор}}</span> 
            </div>
            <!-- <b-field >
                <b-field label="Обозначение:" horizontal custom-class="field_label"><span class="bold">{{item.Обозначение}}</span>
                </b-field>
                <b-field label="Деталь: " horizontal custom-class="field_label"><span class="bold">{{item.Деталь}}</span>
                </b-field>
            </b-field>
            <b-field >
                <b-field label="Изделие: "  horizontal custom-class="field_label"><span class="bold props_val">{{item.Изделие}}</span>
                </b-field>
                <b-field label="Конструктор: " horizontal custom-class="field_label"><span class="bold">{{item.Конструктор}}</span>
                </b-field>
            </b-field> -->
        </div>
        </div>
        <div class="bottom_buttons">
        <b-button size="is-small" @click="data.open_draw(data.active_item)">
Ок
</b-button>
        <b-button size="is-small" @click="data.isactive = false" class="cancel_button">
Отмена
</b-button>
        <b-loading :is-full-page="false" v-model="data.isLoading" :can-cancel="false"></b-loading>
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

.modal_area{
  display: flex;
  flex-direction: column;
  padding: 10px;
  overflow: hidden;
  width: 1000px;
  height: 660px;
}

.select_box_input{
  margin-bottom: 5px;
  min-height: 30px;
  outline: none;
}

.item_list {
  padding: 5px;
  flex-grow: 1;
  background: white;
  border: 1px solid gray;
  overflow: auto;
}

.table_row_new{
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    user-select: none;
    padding: 5px;
}

.choosen_par{
    /* background: floralwhite; */
    background-color: rgb(210, 210, 210);
}

.items_val{
    display: flex;
}

.props_val{
    overflow: hidden;
    width: 600px;
    text-overflow: ellipsis;
}

.field.is-horizontal{
    align-items: center;
}

.marg{
    margin-right: 5px;
}
</style>