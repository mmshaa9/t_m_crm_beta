<template>
    <div class="detalis">
        <div class="detalis_box">							
            <div id="detail_list">		
                <div class="detail_filtering">
                    <input type="search" class=" tm_input" v-model="filter_text" @input="detali_filter" 
                        placeholder="Поиск">
                   <div class="filter_butt" v-if="data.hiden_teg">
                        <button @click="filter2(tag)"  v-for='(tag, index) in tags' :key="index" class="" :class="{active_tag: filter_project == tag}">{{tag}}
                        </button>
                   </div>
                </div>								

                <div class="detail_list_container1 list_dan">
                    <div class="detail_item bold table">
                        <span>№</span>
                        <span @click="sort('Формат')">Фор.</span>
                        <span @click="sort('Раздел')">Раздел</span>
                        <span @click="sort('Проект')">Проект</span>
                        <span @click="sort('Наименование')">Наименование</span>
                        <span @click="sort('Количество')">Кол.</span>
                        <span @click="sort('Длина')">Длина</span>
                        <span @click="sort('Диаметр')">Диаметр</span>
                        <span @click="sort('Толщина')">Толщина</span>
                        <span @click="sort('Ширина')">Ширина</span>
                    </div>									
                </div>									

                <div class="detail_list_container">
                    <div v-for="(detail, index) in item.details" :detail="detail" :id="'btn-draw-' + detail.detail_id" tabindex="0" class="detail_item">
                        <span>{{index + 1}}</span>
                        <span class="format">
                            {{detail.detail_format}}
                           <open-draws :dop="detail"></open-draws>
                        </span>
                        <span>{{detail.detail_razdel}}</span>
                        <span>{{detail.detail_project}}</span>
                        <span>{{detail.detail_name}}</span>
                        <span>{{detail.detail_count}}</span>										
                        <span>{{detail.SW_Длина_заготовки}}</span>
                        <span>{{detail.SW_Диаметр_заготовки}}</span>
                        <span>{{detail.SW_Толщина_заготовки}}</span>
                        <span>{{detail.SW_Ширина_заготовки}}</span>
                    </div>
                </div>								
            </div>							
        </div>	
    </div>
</template>

<script>
    import window from "@/components/window.vue"
    import open_draws from "@/components/open_draws.vue"
    export default ({
        props:['data'],

        components: {
            'window': window,
            'open-draws': open_draws
        },


        data() { 
            return{
                filter_text: '',
                filter_text_memory: { value: undefined },
                filter_project: 'Все',
                filter_column: 'Раздел',
                filter_reverse: false,
                tags:['Все', 'Плазма', 'Токарка', 'Сортамент', 'Детали', 'Покупные', 'Сборки', 'Шпонки','Эрозия' ,'Литье', 'Вальцовка'],
            }
        },
        computed:{
            item(){
                return this.data.item
            },
            
        },
        mounted(){
            this.filter_project = this.data.division
            this.loadDetails()
        },

        methods:{

            loadDetails: function () {
                this.$_send_(
                    {   
                        cmd: 'producton_get_product_detail_list', 
                        item_id: this.item.id, 
                        filter: this.filter_text, 
                        filter_project: this.filter_project, 
                        column: this.filter_column,
                        reverse: this.filter_reverse, 
                        t: new Date() 
                    }, 
                    response=>{
                        this.item.details = response.data
                    }
                )
            },

            unloadDetails: function (item) {
                item.details = []
            },

            detali_filter: function (par) {
                // фильтр по поиску            
                this.loadDetails(item)
            },

            filter2: function (par) {
                // фильтр по тегам
                this.filter_project = par
                this.filter_column = 'Раздел'
                this.filter_reverse = false
                this.loadDetails()
            },

            sort: function (par) {
                if (this.filter_column == par) {
                    if (this.filter_reverse == false) {
                        this.filter_reverse = true
                    } else {
                        this.filter_reverse = false
                    }
                } else {
                    this.filter_reverse = false
                }

                this.filter_column = par

                this.loadDetails()
            }
        }

    })
</script>

<style scoped>
    .detalis {
        width: 70vw;
        height: 70vh;
    }
    .link:hover{
        text-decoration: underline;
        cursor:pointer;
    }

    #detail_list{				
        background: #ececec;
        height: 100%;
        margin:0px;
        font-size: 12pt;
        border-radius: 5px;
        cursor: pointer;
        display:flex;
        flex-direction: column;
        justify-content: stretch;
        align-content: stretch;
        color: black;
    }


    .detail_filtering{
        margin:0px;
        padding:0px;
        margin-bottom: 10px;
        display: flex;
        flex-direction: column;
    }

    .filter_butt>button{ 
        border:none;
        outline:none;
        cursor: pointer;
        padding: 0px;
        text-transform: lowercase;
    }

    .filter_butt>button:hover{
        text-decoration: underline;
    }

    .filter_butt>button:before{
        content: '#'
    }

    .detail_list_container{
        padding: 5px;	
        margin:0px;
        /* margin-top:5px; */
        height: 100%;				
        /* border:1px solid gray; */
        cursor: pointer;
        overflow-y: scroll;
        padding-top: 0px;
        
    }
    
    .detail_list_container1{	
        padding: 5px;	
        padding-top: 0px;
        padding-bottom: 0px;
    }



    .detail_item{
        display:flex;			
        /* width:calc(100% - 10px);	*/
        border:1px solid gray;
        outline: none;
        background: white;
        cursor: pointer;
        margin-bottom: -1px;
        position: relative;
    }


    .detail_item>span{
        padding: 0px;
        text-align: left;
        white-space: nowrap; /* Запрещаем перенос строк */
        overflow: hidden; /* Обрезаем все, что не помещается в область */		    
        text-overflow: ellipsis; /* Добавляем многоточие */
        border-right: 1px solid lightgray;
        padding-right: 5px;
        margin-left: 5px;
    }

    .detail_item>span:nth-child(1), .detail_item>span:nth-child(2){
        width:55px;
        max-width: 55;
        min-width: 55px;
        text-align: right;		
    }
     .detail_item>span:nth-child(3){
        width:120px;
        max-width: 120px;
        min-width: 120px;
        /* text-align: right;		 */
    }
    .detail_item>span:nth-child(4){
        width:90px;
        max-width: 90;
        min-width: 90px;
        /* text-align: right;		 */
    }

    .detail_item>span:nth-child(5){
        /* width:auto;				 */
        /* min-width: 455px; */
        flex-grow: 1;
    }


    .detail_item>span:nth-child(6), .detail_item>span:nth-child(7), .detail_item>span:nth-child(8),.detail_item>span:nth-child(9), .detail_item>span:nth-child(10){
        width:90px;
        max-width: 90px;
        min-width: 90px;
        text-align: right;		
    }


    .detail_item>span:last-child{
        border:none;
        padding-right: 5px;

    }

    .detail_list_container>.detail_item:hover {
        background: rgb(235,235,235);

    }   
    .tm_input{
        margin: 5px 10px 0 5px;
        height: 30px;
        min-width: 60px;
        max-width: 100%;
    }
    .filter_butt{
        display: flex;
        width: 50%;
        justify-content: space-around;
        margin-left: 5px;
    }
    .detalis_box{
        height: 100%;
        padding-bottom: 5px;
    }
    .icons_hover:hover {
        box-shadow: orange;
        text-shadow: 0px 0px 10px orange;
    }
    .format{
        display: flex;
        justify-content: flex-end;
    }

    .detail_list_container1{
        margin-right: 17px;
    }

    .active_tag{
        color: orange
    }
</style>
