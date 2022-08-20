<template>
    <div class="manufacture_list top_list" :class="{grey: item.ready == -1, category4: item.category_id == 4, category5: item.category_id == 5, category2: item.category_id == 2}">
        <div :id="'order-'+ item.order_id +  '-item-' + item.id" tabindex="0">
            <div class="bold">
                {{item.name}}
            </div>
            <div class="isdelie_props_container">					
                <div class="isdelie_prop_name">
                    Категория:
                </div>
                <div class="isdelie_prop_value">
                    {{item.category_name}}
                </div>
                <div class="isdelie_prop_name">
                    , количество:
                </div>
                <div class="isdelie_prop_value">
                    {{item.count}}
                </div>
                <div class="isdelie_prop_name">
                    <slot name="deadline"></slot>
                </div>
                <div :class="{red: item.order_deadline_over == -1 || item.item_deadline_over == -1}" class="isdelie_prop_value">
                    <slot name="order_deadline"></slot>
                    <slot name="item_deadline"></slot>
                </div>
                <div class="isdelie_prop_name">
                    , ID:
                </div>
                <div class="isdelie_prop_value">
                    {{item.id}}
                </div>	
            </div>
            <slot name="ready_status"></slot>
            <slot name="butt"></slot>
        </div>
        
        <div class="ww" :class="{ hasError : (item.constructor == 'Не назначен') }">
            {{item.constructor}}
        </div>						
        <slot name="manager"></slot>
        <slot name="drawings"></slot>
        <slot name="delivery"></slot>   
        <slot name="plazma_details"></slot>   
        <slot name="erozia_details"></slot> 
        <slot name="casting_details"></slot> 
        <div :class="{ grey : (item.tech_plazma_status == 'Не требуется') }"> 
            <!-- {{item.tech_plazma_status}} -->
            <slot name="plazma_status"></slot>
        </div>
        <div :class="{ grey : (item.tech_tokarka_status == 'Не требуется') }">
            <!-- <div>{{item.tech_tokarka_status}}</div> -->
            <slot name="tokarka_status"></slot>
        </div>
        <div  :class="{ grey : (item.tech_raspil_status == 'Не требуется') }">
            <!-- {{item.tech_raspil_status}} -->
            <slot name="raspil_status"></slot>
        </div>
        <slot name="erozia_status"></slot>
        <slot name="casting_status"></slot>
        <slot name="btn"></slot>      
        <slot></slot>
    </div>
</template>

<script>
    export default ({
        props:['item'],
        components: {
         
        },


        data() { 
            return{
          
            }
        },

        methods:{
          
        }

    })
</script>


<style scoped>
    .manufacture_list{
        display: flex;
        background: white;
        width: 100%;
        /* height: 130px; */
        min-height: 70px;
        font-family: calibri;
        justify-content: space-between;
        border: 1px solid lightgray;
        padding: 3px 5px;
    }
    .isdelie_props_container{
        display:flex;
        flex-direction: row;
        align-content: flex-start;
        margin: 0px;
        padding: 0px;
        flex-wrap: wrap;
    }
    .isdelie_prop_name{
        display:flex;
        margin: 0px;
        padding: 0px;
    }
    .isdelie_prop_value{
        display:flex;
        margin: 0px;
        padding: 0px;
        margin-left: 5px;
        font-weight: bold;
    }
    .isdelie_prop_value1 {
        display:flex;
        margin: 0px;
        padding: 0px;
        margin-left: 5px;
        font-weight: bold;
        color: red;
    }
    #detail_list{				
        background: white;
        height: 100%;
        margin:0px;


        font-size: 12pt;
        border-radius: 5px;
        cursor: pointer;


        display:flex;
        flex-direction: column;
        justify-content: stretch;
        align-content: stretch;
    }

    .detail_header_container{
        display:flex;
        flex-direction: row;

        margin:0px;
        padding: 0px;
        align-content: center;
        justify-content: center;
        padding: 5px;

        background: #C77E30;
    }

    .detail_header_container>div{
        text-align:center;font-weight: bold;				
        width: 100%;
        margin:0px;
        padding: 0px;				
        text-decoration:none;
        color:white;
    }

    .detail_header_container>button{
        border:1px solid white;
        outline:none;				
        width:20px;				
        margin:0px;
        padding: 0px;
        color:white;				
        font-weight: bold;
        border-radius: 5px;
        cursor: pointer;
    }
   .top_list> div:nth-child(1) {
        flex-grow: 1;
    }
    .ww {
        width: 130px;
        -ms-flex-negative: 1;
        flex-shrink: 0;
        flex-grow: 0;
        flex-basis: 1;
        margin: 0;
        padding: 4px 4px;
    }

    >>>.loading-icon:after{
        width: 1em;
        height: 1em;
        top: calc(50% - .5em);
        left: calc(50% - .5em);
        border-right-color: orange;
        border-top-color: orange;
    }

    .red{
        color: red;
    }

    >>> .loading-background {
        border-radius: 8px;
    }

    .category2{
        background:SandyBrown;				
    }

    .category5{
        background:rgba(0,255,0,0.1);				
    }

    .category4{
        background:rgba(0,0,255,0.2);				
    }
</style>