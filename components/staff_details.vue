<template>
    <div class="content_staff_details_line">
        <div class="staff_details_line_block_1">{{worker.name}}</div>
        <div class="staff_details_line_block_2">12.12.12</div>
        <div class="staff_details_line_block_3">1488</div>
        <div class="staff_details_line_block_4">123456</div>
        <div class="staff_details_line_block_5">1111111 p.</div>
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
    .staff_details_line_block_1 {
        width: 30%;
    }
    .staff_details_line_block_2 {
        width: 20%;
    }
    .staff_details_line_block_3 {
        width: 20%;
    }
    .staff_details_line_block_4 {
        width: 20%;
    }
    .staff_details_line_block_5 {
        width: 10%;
    }
</style>