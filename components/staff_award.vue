<template>
    <div class="page_staff_award_one">
        <div class="staff_award_title">
            <div class="staff_award_line_1">Сотрудник</div>
            <div class="staff_award_line_2">План</div>
            <div class="staff_award_line_3">Доход по фирме</div>
            <div class="staff_award_line_4">Доход по сотруднику</div>
            <div class="staff_award_line_5">Оклад</div>
            <div class="staff_award_line_6">Процент</div>
            <div class="staff_award_line_7">Всего</div>
        </div>
        <div class="staff_award_content">
            <div class="staff_award_line_1">1</div>
            <div class="staff_award_line_2">1</div>
            <div class="staff_award_line_3">1</div>
            <div class="staff_award_line_4">1</div>
            <div class="staff_award_line_5">1</div>
            <div class="staff_award_line_6">1</div>
            <div class="staff_award_line_7">1</div>
        </div>
    </div>
</template>

<script>
    module.exports = ({
        props:['data'],
        data(){
            return {
               
            }  
        },    
    })
</script>

<style scoped>
    .page_staff_award_one {
        display: flex;
        width: 1000px;
        flex-direction: column;
    }
    .staff_award_title {
        padding: 0px 5px;
        display: flex;
        border-top: 1px solid #929397;
        background: rgb(225, 225, 225);
    }
    .staff_award_content {
        display: flex;
        background: white;
        border-top: 1px solid #929397;
        padding: 5px;
    }   
    .staff_award_line_1 {
        width: 20%;
    }
    .staff_award_line_2 {
        width: 10%;
    }
    .staff_award_line_3 {
        width: 20%;
    }
    .staff_award_line_4 {
        width: 20%;
    }
    .staff_award_line_5 {
        width: 10%;
    }
    .staff_award_line_6 {
        width: 10%;
    }
    .staff_award_line_7 {
        width: 10%;
    }

</style>