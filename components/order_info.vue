<template>
    <div class="modal_info_area">
        <div>
            <span class="info_label">Заказ:</span>
            <span class="bold">{{data.номер_заказ}}</span>
        </div>
        <div>
            <span class="info_label">Клиент:</span>
            <span class="bold">{{data.Название}}</span>
        </div>
        <div>
            <span class="info_label">Менеджер:</span>
            <span class="bold">{{data.Сотрудник}}</span>
        </div>
        <div>
            <span class="info_label">Отгрузка:</span>
            <span class="bold">{{data.отгрузка_дата1}}</span>
        </div>
        <div>
            <span class="info_label">Статус:</span>
            <span class="bold">{{data.статус}}</span>
        </div>
    </div>
</template>

<script>
export default {
    props:{
        data:{
            type: Object
        }
    }
}
</script>

<style scoped>

    .modal_info_area {
        padding: 6px;
        display: flex;
        flex-direction: column;
        flex-grow: 1;
        width: 20vw;
        background: white;
    }

    .info_label {
        width: 80px;
        margin-right: 5px;
        display: inline-block;
    }
</style>