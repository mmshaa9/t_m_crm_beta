<template>
    <div class="demands_container">
        <div class="demands_main_header">
            <div>Выберите заявку для создания заказа</div>
            <input class="tm_input" v-model="filter_text" placeholder="Поиск по заявкам" type="search">
        </div>
        <div class="demands_wrapper">
            <DemandItem v-for="(demand, index) in filter" :client="demand" :key="index" :all="true" @dblclick.native="$emit('create_order', demand)" @click.native="active_id = demand" :class="{active: active_id.заявка_код == demand.заявка_код}"></DemandItem>
        </div>
        <div class="demands_buttons">
            <b-button size="is-small" @click="$emit('create_order', active_id)">Ок</b-button>
            <b-button size="is-small" @click="$parent.obj.isactive = false">Отмена</b-button>
        </div>
    </div>
</template>

<script>


import DemandItem from "@/components/demand_item.vue"

export default {
    props:{
        data:{
            type:Array
        }
    },

    components:{
        DemandItem
    },

    data(){
        return{
            filter_text:'',
            active_id: ''

        }
    },

    computed:{
        filter(){
            if (this.filter_text != null) {
                if (this.filter_text.length > 0) {
                    return this.data.filter((e) => {
                        return e.название_формат
                            .toLowerCase()
                            .includes(this.filter_text.toLowerCase());
                    });
                }
            }
            return this.data;
        }
    },

    methdods:{

    }
}
</script>

<style scoped>
    .demands_container {
        width: 28vw;
        height: 60vh;
        overflow: hidden;
        display: flex;
        flex-direction: column;
        background: whitesmoke;
    }

    .demands_main_header{
        display: flex;
        flex-direction: column;
        padding: 5px;
        margin-bottom: 5px;
        border-bottom: 1px solid gray;
    }

    .demands_wrapper{
        padding: 5px;
        overflow: auto;
        background: papayawhip;
        flex-grow: 1;
        flex-basis: 100px;
    }

    .demands_buttons{
        display: flex;
        flex-direction: row;
        justify-content: flex-end;
    }

    .active {
        opacity: 1;
        cursor: pointer;
        background-color: rgb(210, 210, 210);
    }
</style>