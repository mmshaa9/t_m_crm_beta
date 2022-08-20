<template>
    <div class="staff_award_content">
        <div class="staff_award_line">{{worker.name}}</div>
        <div class="staff_award_line">1</div>
        <div class="staff_award_line">1</div>
        <div class="staff_award_line">1</div>
        <div class="staff_award_line">{{worker.salary}}</div>
        <div class="staff_award_line">1</div>
        <div class="staff_award_line">1</div>
    </div>
</template>

<script>
    module.exports = ({
        props:['conceal', 'worker'],
        data(){
            return {
               
            }  
        },    
    })
</script>

<style scoped>
    .staff_award_content {
        display: flex;
        background: white;
        border: 1px solid lightgray;
        border-top: none;
    } 
    .staff_award_line {
        padding: 0px 5px;
        width: 20%;
    }
</style>