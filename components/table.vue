<template>
<div class="stat_table">
    <table class="table mat_des_shadow">
        <tr >
            <th v-for="(header, index) in header" :key="index" :style="{minWidth: col_widths != undefined ? col_widths[index]  + 'px': '' }" class="th_header">
{{header}}
</th>
        </tr>
        <tr v-for="data in data" >
            <!-- <td class="td_table">{{index + 1}}</td> -->
            <td v-for="d in data" class="td_table" >
{{d}}
</td>
            <!-- <td class="td_table">{{stat.Сотрудник}}</td>
            <td class="td_table">{{stat.НАЧАЛО}}</td>
            <td class="td_table">{{stat.КОНЕЦ}}</td> -->
        </tr>
        <!-- <tr>
            <td class="th_header bold"  colspan="3">Всего</td>
            <td class="th_header bold">{{demand_close}}</td>
            <td class="th_header bold">{{demand_get}}</td>
        </tr> -->
    </table>
</div>
</template>

<script>
module.exports = ({
    props:['header', 'data', 'col_widths'],
    data(){
        return{

        }
    },
    
    methods:{

    }
})
</script>

<style scoped>

.stat_table{
    padding: 10px;
    overflow: auto;
    height: calc(100% - 40px);
    padding-left: 15px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.th_header {
    border: 1px solid lightgray;
    border-right: unset;
    border-left: unset;
    background: lightgray;
}

.td_table {
    border: 1px solid lightgray;
    border-right: unset;
    border-left: unset;
}

.table{
    width: 70%;
    z-index: 0;
}
</style>