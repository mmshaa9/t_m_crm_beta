<template>
    <div class="inventory">
        <div class="inventory_header">
            <div class="">Дата, время</div>
            <div class="">Наименование</div>
            <div class="">Длина, мм.</div>
            <div class="">Тек. остаток, шт.</div>
            <div class="">Нов. остаток, шт.</div>
        </div>
        <div class="inventory_wrapper">
            <div
                class="inventory_content"
                v-for="(item, index) in items"
                :key="index"
            >
                <div class="">{{ item.дата_изменения }}</div>
                <div class="">{{ item.Наименование }}</div>
                <div class="">{{ item.Длина * 1 }}</div>
                <div class="">{{ item.Остаток2 * 1 }}</div>
                <div class="">
                    <span>{{ item.Новый_остаток * 1 }}</span>
                    <div class="icons">
                        <i class="fas fa-plus" @click="plus(item)"></i>
                        <i class="fas fa-minus" @click="minus(item)"></i>
                    </div>
                </div>
            </div>
        </div>
        <div class="bottom_buttons">
            <button class="button" @click="confirm_save">Применить изменения</button>
        </div>
    </div>
</template>

<script>
import { mapActions } from 'vuex'
export default {
    data() {
        return {
            items: [],
            user: this.$store.getters.USER_ID,
        };
    },

    mounted() {
        this.load_data();
    },

    methods: {

        ...mapActions([
            'show_alert',
            'confirm'
        ]),
        load_data() {
            this.$_send_(
                {cmd: "metall_store_get_stocktaking_list", user: this.user},
                (response) => {
                    this.items = response.data;
                }
            );
        },

        plus(item) {
            item.Новый_остаток++;
        },

        minus(item) {
            item.Новый_остаток--;
        },

        confirm_save(){
            let items = [];
            this.items.forEach((el) => {
                if (el.Остаток2 - el.Новый_остаток != 0) {
                    items.push(el);
                }
            });
            if(items.length == 0) return this.$alert("Не было внесено изменений", 'is-warning')
            this.confirm({
                text: "Вы действительно хотите применить изменения?",
                confirm_action: () => {this.save(items)}
            })
        },

        save(items) {
            this.$_send_({cmd:"metall_store_update_stocktaking_list", items: JSON.stringify(items), user: this.user}, response=>{
                if(response.data.result == "-ok"){
                    this.show_alert({text:"Операция успешно завершена", action: ()=>{
                        window.open(this.$get_base64_file(response.data.bill, 'application/pdf'))
                        this.$parent.obj.isactive = false
                        this.$emit('update')
                    }})
                }else{
                    this.show_alert({text:"Не удалось применить изменения, попробуйте позже", action: ()=>{return}})
                }
            })
        },
    },
};
</script>

<style scoped>
.inventory {
    width: 50vw;
    height: 50vh;
    overflow: hidden;
    display: flex;
    flex-direction: column;
}

.inventory_wrapper {
    overflow-y: scroll;
    flex-grow: 1;
}

.inventory_header {
    display: flex;
    font-weight: 600;
    padding: 2px 0px 2px 20px;
    border-bottom: 1px solid lightgrey;
    margin-right: 17px;
}
.inventory_header > div {
    width: 160px;
}
.inventory_header > div:nth-child(2) {
    flex-grow: 1;
}
.inventory_content {
    display: flex;
    padding: 2px 0px 2px 20px;
    background: white;
    border-bottom: 1px solid lightgrey;
}
.inventory_content > div {
    width: 160px;
}

.inventory_content > div:nth-child(2) {
    flex-grow: 1;
}

.inventory_content > div:nth-child(5) {
    display: flex;
    justify-content: space-between;
}
.icons {
    margin-right: 25px;
}
.fas {
    padding: 3px 5px;
    cursor: pointer;
}

.bottom_buttons{
    display: flex;
    flex-direction: row;
    justify-content: flex-end;
}

.bottom_buttons>*{
    height: 30px;
}
</style>
