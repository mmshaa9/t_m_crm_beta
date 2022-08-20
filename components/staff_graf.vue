<template>
    <div class="background_box" v-if="worker.open_graf">
        <div class="graph_page" >
            <div class="graph_block_head" @click="worker.open_graf=false">
                <div class="close_butt">
                    <div class="close_text">График</div>
                    <button class="close_btn">&#10006;</button>
                </div>
            </div>
            <div class="graph_block_one">
                <div class="graph_block_one_numer">{{worker.number}}</div>
                <div class="graph_block_one_name">{{worker.name}}</div>
                <div class="graph_block_one_photo">
                    <i class="far fa-user"></i>
                </div>
            </div>
            <div class="graph_block_two">
                <div class="month">
                    <div class="week">
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">1</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">2</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">3</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">4</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">5</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">6</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">7</div>
                            <div class="day_opt">2</div>
                        </div>
                    </div>
                    <div class="week">
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">8</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">9</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">10</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">11</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">12</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">13</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">14</div>
                            <div class="day_opt">2</div>
                        </div>
                    </div>
                    <div class="week">
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">15</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">16</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">17</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">18</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">19</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">20</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">21</div>
                            <div class="day_opt">2</div>
                        </div>
                    </div>
                    <div class="week">
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">22</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">23</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">24</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">25</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">26</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">27</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">28</div>
                            <div class="day_opt">2</div>
                        </div>
                    </div>
                    <div class="week">
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">29</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">30</div>
                            <div class="day_opt">2</div>
                        </div>
                        <div class="day" @click="conceal.hidden_graph_mini_card=!conceal.hidden_graph_mini_card">
                            <div class="day_numer">31</div>
                            <div class="day_opt">2</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="result">
                <div class="result_text">Всего</div>
                <div class="result_numer">1111</div>
            </div>
        </div>
        <!-- Мини карточка графика -->
     <staff-graph-mini-card :conceal="conceal"> </staff-graph-mini-card>
    </div>
</template>

<script>
    import staff_graph_mini_card from "@/components/staff_graph_mini_card.vue"

    export default ({
        components:{
            'staff-graph-mini-card': staff_graph_mini_card,
        },
        props:['conceal', 'worker'],
        data(){
            return {
            }  
        },  
        
        methods: {         
           
        },
    })
</script>

<style scoped>
    .background_box{
        display: flex;
        justify-content: center;
        align-items: center;
        top: 0;
        left: 0;
        height: 100%;
        width: 100%;
        background: rgba(0, 0, 0, 0.70);
        position: fixed;
        z-index: 50;
    }
    .head_staff {
        display: flex;
        justify-content:space-between;
        align-items: center;
        padding: 2px 8px;
        font-weight: 400 !important;
    }
    .close_btn{
        background: local;
        border: none;
        padding:  3px 5px;
    }
     .close_btn:hover{
        background: red;
        border-radius: 3px;
    }
    button{
        cursor: pointer;
    }
    .active_row{
        background: gainsboro;
    }
    /* --------------------------------Карточка графика */
    .graph_page {
        margin-top: 3%;
        height: max-content;
        display: inline-flex;
        flex-direction: column;
        background: white;
        border: 1px solid rgb(216, 216, 216);
    }

    .graph_block_one {
        display: flex;
        justify-content: space-evenly; 
        align-items: center;
        background: rgb(243, 243, 243);
        border-bottom: 1px solid  #929397;
        padding: 5px 0 5px 0;
        font-weight: 600;
    }
   
    .close_butt{
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin: 0;
        border-bottom: 1px solid  #929397;
        background: rgb(243, 243, 243);
        font-weight: 400;
        padding: 2px 5px;
    }
    .graph_block_two {
        padding: 4px;
    }
    .week {
        display: flex;
    }

    .day {
        display: flex;
        flex-direction: column;
        width: 50px;
        background: rgb(192, 192, 192);
        margin: 5px;
        font-weight: 400;
        cursor: pointer;
    }

    .day_numer {
        padding: 3px 0 3px 0;
        background: gainsboro;
        width: 100%;
        text-align: center;
    }

    .day_opt {
        padding: 5px 0 5px 0;
        width: 100%;
        text-align: center;
    }
    .result{
        display: flex;
        justify-content: space-between;
        padding: 3px;
        background: rgb(243, 243, 243);
        border-top: 1px solid  #929397;
        font-weight: 600;
    }
    .fa-user,
    .fa-file-invoice-dollar,
    .fa-industry,
    .fa-building{
        font-size: 28px;
        padding-top: 5px;
    }
</style>