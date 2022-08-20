<template>
    <div class="list" :class="ready" title="Нажмите для редактирования">
        <div class="container_list_2 table_row_my" @click="open_card(order)">
            <div class="order_num_date_row">
                <div class="mobile-vue">ЗАКАЗ (№, дата):</div>
                <div class="mobile_class_vue special-class mobile_display">
                    {{ order.номер_заказ + " " + order.заказ_дата }}
                    <div
                        v-if="order.заказ_отгрузка == 'Заказ готов к отгрузке'"
                        class="greeny"
                    >
                        {{ order.заказ_отгрузка }}
                    </div>
                </div>
            </div>
            <div class="client_row">
                <div class="mobile-vue">НАЗВАНИЕ:</div>
                <div class="mobile_class_vue">
                    {{ order.Название }}
                </div>
            </div>
            <div class="summ_row">
                <div class="mobile-vue">СУММА:</div>
                <div class="mobile_class_vue">
                    {{ order.заказ_сумма }}
                </div>
            </div>
            <div class="pre_summ_row">
                <div class="mobile-vue">ПРЕДОПЛАТА:</div>
                <div class="mobile_class_vue">
                    {{ order.заказ_сумма }}
                    {{ order.предоплата + " " + order.предоплата_дата }}
                </div>
            </div>
            <div class="time_exec_row">
                <div class="mobile-vue">ВРЕМЯ ИСПОЛ.:</div>
                <div class="mobile_class_vue">
                    {{ order.дни_количество }}
                </div>
            </div>
            <div
                v-if="!parseInt(order.дедлайн) && order.статус_код != '4'"
                class="date_load_row red"
            >
                <div class="mobile-vue">ДАТА ОТГРУЗКИ:</div>
                <div class="mobile_class_vue">
                    {{ order.отгрузка_дата1 }}
                </div>
            </div>
            <div v-else class="date_load_row">
                <div class="mobile-vue"></div>
                <div class="mobile_class_vue">
                    {{ order.отгрузка_дата1 }}
                </div>
            </div>
            <div class="duty">
                <div class="mobile-vue">ДОЛГ:</div>
                <div class="mobile_class_vue">
                    {{ order.долг }}
                </div>
            </div>
            <div class="manager_row">
                <div class="mobile-vue">МЕНЕДЖЕР:</div>
                <div class="mobile_class_vue">
                    {{ order.Сотрудник }}
                </div>
            </div>
            <div class="order_status_row">
                <div class="mobile-vue">СТАТУС ЗАКАЗА:</div>
                <div class="mobile_class_vue">
                    {{ order.статус }}
                </div>
            </div>
            <div @click.stop.prevent class="item_id_row">
                <div class="mobile-vue">ID ЗАКАЗА:</div>
                <div class="mobile_class_vue">
                    {{ order.изделия_список }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ["order"],

    computed: {
        ready: function () {
            if (this.order.статус_код == "4") {
                return "ready";
            } else {
                return false;
            }
        },
    },
    methods: {
        open_card: function (order) {
            this.$store.dispatch("SET_MARKET_ORDER", order.Код);
            this.$store.dispatch("SET_MARKET_CHAT", order.беседа_код);
            this.$store.dispatch("SET_MARKET_DEMAND", order.заявка_код);
            this.$store.dispatch("SET_PREVIOUS_PAGE", "orders");
            let card_order = this.$router.resolve({
                name: "marketing_order_card",
            });
            window.open(card_order.href, "_blank");
        },
    },
};
</script>

<style scoped>
.ready {
    opacity: 0.5;
}

.list:hover {
    opacity: 1;
    cursor: pointer;
    background: floralwhite;
    border-color: black;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
}

.greeny {
    color: #257953;
    /* color: #69cea0  */
}

/*...............Медиа запросы.................*/

@media (min-width: 1024px) {
    .mobile-vue {
        display: none;
    }
}

@media (max-width: 1024px) {
    .mobile_display {
        display: flex;
    }
}
</style>
