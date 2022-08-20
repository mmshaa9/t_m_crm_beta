<template>		
    <div class="item">
        <div v-if="!item.anton_seen" class="pulse"></div>
        <!-- заголовок -->
        <div class="item_header">
            <div class = "item_header_block_1">				
                <div class="order_id" :title="item.client+ '\r\n'+ item.contact">
{{item.order_id}}
</div>
            </div>

            <div class = "item_header_block_2">
                <div class="item_header_name" :title="item.name">
{{item.name + ' ' + item.category_id}} {{item.category_id}}
</div>
                <hr class="line_dan">
                <div  class="item_header_describtion">
{{item.id}}, {{item.count}}шт., {{item.deadline}}
</div>			
            </div>
        </div>
        <!-- блок с иконками -->
        <div class="icons_dan" :class="{category4: item.category_id == 4, category5: item.category_id == 5}">				
            <div  class="icon fas fa-file-alt" :class="sw_status"></div>
            <div class="icon fas fa-dolly" :class="byed_status"></div>
            <div class="icon fas fa-stream" :class="metall_status"></div>
            <div class="icon" :class="raspil_status">
                <svg viewBox="0 0 512 512"  class="box_posD"  :class="raspil_status">
                    <g transform="translate(0,512) scale(0.1,-0.1)">
                        <path d="M2496 4439 c-838 -97 -1504 -733 -1626 -1554 -34 -224 -23 -512 26 -710 42 -170 132 -393 204 -505 17 -26 30 -52 30 -57 0 -6 -31 -44 -68 -84 		-134 -144 -130 -164 56 -337 65 -60 127 -114 138 -120 24 -13 83 -2 119 22 13 9 59 57 102 108 100 118 174 198 183 198 4 0 10 -14 14 -31 3 -17 19 -44 35 -60 25 -25 37 -29 83 -29 29 0 80 7 113 15 81 19 79 20 94 -22 51 -141 100 -203 162 -203 14 0 67 20 118 45 51 25 94 45 96 45 2 0 31 -34 65 -75 111 -136 156 -139 283 -18 29 27 57 53 64 57 7 4 45 -16 92 -48 92 -63 111 -70 155 -62 40 8 71 40 126 131 23 39 44 71 45 73 1 2 44 -11 95 -28 51 -16 104 -30 117 -30 67 0 108 55 140 188 l17 72 113 0 113 0 38 34 37 35 -1 115 -1 116 41 10 c23 6 71 20 108 31 114 33 133 90 78 244 l-29 84 78 45 c154 89 167 139 72 278 l-59 86 73 82 c117 132 111 175 -42 287 -35 25 -63 47 -63 47 0 1 20 42 45 90 79 156 61 203 -102 271 -51 20 -90 41 -88 45 3 4 12 49 20 99 13 72 14 99 5 126 -12 38 -50 70 -94 81 -27 7 -25 10 122 158 156 157 172 178 172 221 0 43 -23 75 -137 186 -124 122 -147 139 -186 139 -42 0 -64 -15 -143 -92 l-71 -70 -144 73 c-230 115 -405 169 -639 198 -96 12 -291 12 -394 0z m163 -1486 c102 -32 181 -135 181 -233 0 -61 -38 -138 -89 -182 -138 -118 -363 -74 -437 86 -93 199 120 402 345 329z"/>
                    </g>
                </svg>
            </div>

            <div class="icon" :class="plazma_status">
                <svg viewBox="0 0 512 512" class="box_posD" :class="plazma_status">
                    <g transform="translate(0,512) scale(0.1,-0.1)">
                        <path d="M1930 4830 c0 -5 -9 -10 -19 -10 -11 0 -29 -12 -40 -26 -20 -25 -21 -39 -21 -273 0 -135 3 -256 6 -267 9 -36 46 -44 194 -44 l140 0 0 -265 0 -265 -85 0 c-50 0 -85 -4 -85 -10 0 -5 -7 -10 -15 -10 -32 0 -38 -42 -43 -320 -5 -299 0 -382 27 -409 9 -9 23 -32 31 -51 8 -19 16 -37 20 -40 3 -3 14 -24 25 -47 11 -24 23 -43 27 -43 3 0 9 -13 13 -30 4 -16 10 -30 14 -30 5 0 15 -17 25 -37 18 -42 48 -95 57 -103 3 -3 12 -22 20 -42 7 -21 17 -38 23 -38 5 0 7 -3 3 -6 -3 -4 5 -22 20 -40 14 -19 22 -34 19 -34 -4 0 8 -24 27 -52 18 -29 47 -82 66 -118 40 -79 64 -123 72 -130 3 -3 13 -25 23 -50 10 -25 24 -52 32 -61 21 -23 16 -1075 -5 -1088 -7 -5 -11 -13 -7 -19 3 -6 2 -13 -4 -17 -27 -17 8 -96 57 -126 47 -29 124 -23 160 14 21 21 26 34 24 74 -1 26 -4 289 -7 583 -4 406 -2 544 7 573 7 20 16 37 21 37 4 0 8 5 8 11 0 6 9 24 21 40 11 16 17 29 14 29 -4 0 0 7 7 15 29 32 38 45 38 53 0 5 9 21 21 36 11 14 17 26 12 26 -4 0 -3 4 2 8 6 4 16 18 23 32 7 14 24 43 37 65 14 22 34 58 46 80 48 93 65 123 87 152 12 17 22 34 22 38 0 3 16 31 35 61 19 30 35 57 35 59 0 3 12 24 27 48 37 58 58 95 82 143 10 23 24 45 30 48 15 9 15 676 1 676 -5 0 -10 7 -10 15 0 8 -7 15 -15 15 -8 0 -15 5 -15 10 0 6 -33 10 -80 10 l-80 0 0 265 0 265 136 0 c135 0 174 8 174 35 0 8 5 15 10 15 6 0 10 93 10 258 0 257 0 259 -23 280 -13 12 -30 22 -38 22 -8 0 -20 5 -26 11 -8 8 -13 8 -17 0 -4 -6 -16 -11 -27 -11 -11 0 -24 -11 -31 -25 -6 -14 -15 -25 -20 -25 -4 0 -8 -85 -8 -190 l0 -190 -605 0 -605 0 0 193 c-1 204 -1 207 -52 240 -31 19 -48 22 -48 7z"/>
                        
                        <path d="M1995 1923 c-48 -12 -82 -93 -54 -124 8 -8 23 -33 35 -54 46 -87 59 -110 67 -113 4 -2 7 -8 7 -14 0 -5 8 -22 19 -36 10 -15 32 -52 48 -82 84 -158 145 -249 175 -264 35 -18 58 -21 58 -7 0 5 9 11 20 14 27 7 44 49 36 89 -3 18 -28 71 -56 117 -27 46 -50 88 -50 93 0 4 -4 8 -9 8 -5 0 -12 11 -15 24 -3 14 -12 31 -19 38 -7 7 -20 27 -29 43 -39 75 -140 244 -151 253 -16 14 -57 21 -82 15z"/>
                        
                        <path d="M3130 1914 c-27 -17 -42 -39 -93 -134 -17 -30 -33 -57 -36 -60 -3 -3 -15 -23 -26 -45 -11 -22 -23 -42 -26 -45 -3 -3 -29 -48 -59 -100 -30 -52 -56 -97 -59 -100 -3 -3 -16 -24 -28 -47 -28 -51 -30 -104 -5 -124 44 -35 92 -51 92 -29 0 6 6 10 13 10 17 0 50 36 69 75 12 25 137 241 224 390 13 22 29 52 36 68 7 15 16 27 20 27 4 0 8 15 8 33 -1 67 -78 115 -130 81z"/>
        
                        <path d="M1010 1883 c-34 -23 -40 -33 -40 -71 0 -40 15 -72 35 -72 8 0 15 -4 15 -10 0 -5 4 -10 9 -10 5 0 38 -18 72 -39 76 -48 197 -122 221 -136 10 -5 63 	-38 118 -72 55 -35 104 -63 109 -63 5 0 11 -3 13 -7 2 -7 50 -37 213 -136 11 -7 46 -29 78 -49 32 -21 61 -38 65 -38 6 0 81 -49 92 -60 3 -3 23 -13 45 -24 22 -10 48 -22 58 -27 11 -6 17 -5 17 1 0 5 8 10 18 10 30 0 62 42 62 82 0 28 -7 42 -32 63 -18 15 -41 31 -52 36 -12 5 -41 23 -66 39 -44 28 -156 95 -215 128 -16 9 -32 20 -35 23 -3 3 -21 14 -40 25 -19 10 -37 22 -40 25 -9 10 -242 149 -250 149 -5 0 -10 4 -12 8 -1 5 -30 23 -63 42 -33 19 -81 49 -107 67 -26 18 -49 33 -51 33 -2 0 -41 23 -87 50 -90 54 -112 59 -150 33z"/>
    
                        <path d="M4070 1874 c-25 -14 -52 -32 -60 -39 -8 -6 -43 -27 -77 -46 -35 -18 -63 -37 -63 -41 0 -5 -5 -8 -10 -8 -6 0 -29 -12 -53 -27 -67 -44 -72 -47 -118 	-72 -24 -13 -46 -26 -49 -30 -7 -8 -40 -29 -70 -43 -14 -7 -45 -26 -70 -44 -25 -17 -65 -42 -88 -55 -77 -42 -96 -54 -142 -85 -25 -17 -50 -33 -55 -35 -6 -2 -48 -26 -94 -54 -110 -65 -141 -95 -141 -133 0 -36 34 -82 61 -82 10 0 19 -5 19 -12 0 -9 3 -9 12 0 7 7 19 12 28 12 8 0 44 19 80 43 36 23 79 49 95 57 17 8 32 17 35 20 7 9 109 70 116 70 2 0 10 5 17 10 25 22 215 138 235 145 12 3 28 12 34 19 13 13 246 156 254 156 3 0 10 5 17 10 19 16 77 53 146 94 79 46 91 58 98 99 4 26 0 40 -18 61 -35 39 -83 43 -139 10z"/>
                        
                        <path d="M1454 993 c-21 -23 -25 -35 -21 -67 3 -21 12 -45 19 -53 22 -22 115 -31 376 -39 133 -3 245 -10 248 -15 13 -22 82 -3 116 31 38 39 31 96 -15 126 	-29 19 -57 22 -322 33 -393 15 -371 16 -401 -16z"/>

                        <path d="M3285 1008 c-172 -6 -227 -12 -250 -24 -42 -23 -55 -42 -55 -80 0 -43 59 -94 107 -94 18 0 33 4 33 8 0 5 131 13 291 19 l290 10 30 29 c39 38 41 	93 3 123 -30 24 -59 25 -449 9z"/>

                        <path d="M260 625 l0 -85 1073 0 1073 0 25 -32 c13 -18 32 -46 43 -63 10 -16 21 -32 25 -35 5 -4 49 -68 67 -98 7 -10 73 -18 93 -11 23 9 51 33 51 43 0 4 	12 25 27 49 16 23 30 47 33 52 3 6 18 29 34 53 l29 42 1019 0 1019 0 -3 83 -3 82 -1057 3 -1058 2 -24 -21 c-14 -12 -42 -48 -62 -80 -20 -33 -40 -59 -44 -59 -3 0 -21 22 -39 50 -18 27 -46 63 -62 80 l-31 30 -1114 0 -1114 0 0 -85z"/>
                    </g>
                </svg>
            </div>

            <div class="icon fas fa-database icon_rotate" :class="tokarka_status"></div>			
            <div class="icon fas fa-truck"  :class="shipment_status"></div>
            <div class="icon icon-normal">
{{item.day_to_end * 1}}
</div>
            <div class="isdelie_prop_container">
                <div class="isdelie_prop_name">
Ответственные:
</div>
                <div class="isdelie_prop_value">
{{item.manager}}/{{item.constructor}}
</div>			
            </div>
        </div>				
    </div>
</template>

<script>
module.exports = ({
    props:['item'],
    template: '#isdelie_template',
    
    data: function(){
        return {
            filter_text:'',
            sort_text:'Раздел',
            sort_reverse: false
        }
    },		

    // вычисляемые статусы иконок
    computed: {
        sw_status: function() {     
            return {        			
                'icon-normal': this.item.sw_loaded != -1,
                'icon-good':  this.item.sw_loaded == -1        			
            }
        },

        shipment_status: function(){
            return {
                'icon-normal': this.item.ready_to_shipment != 1, // нужен
                'icon-good': this.item.ready_to_shipment === 1, // доставлен
            }
        },

        byed_status: function(){
            return {
                'hidden': this.item.sw_loaded != -1,
                'icon-disabled': this.item.byed != -1,
                'icon-normal': this.item.byed == -1,
                'icon-good': this.item.tech_byed_done == 1
            }
        },

        metall_status: function(){
            return {        			
                'hidden': this.item.sw_loaded != -1,
                'icon-disabled': this.item.tech_raspil_count + this.item.tech_plazma_count + this.item.tech_tokarka_count == 0, // металл не нужен
                'icon-normal':  this.item.tech_raspil_count + this.item.tech_plazma_count + this.item.tech_tokarka_count > 0, // нужен
                'icon-good': this.item.tech_metall_done == 1
                
            }
        },

        raspil_status: function(){
            //console.log(this.item.id + ': ' + this.item.tech_raspil_code)
            return {        			
                'hidden': this.item.sw_loaded != -1,
                'icon-disabled': this.item.tech_raspil_count == 0, // металл не нужен
                'icon-normal':  this.item.tech_raspil_count > 0, // нужен
                'icon-good': this.item.tech_raspil_code == 3, // доставлен        			
            }
        },

        plazma_status: function(){
            return {        			
                'hidden': this.item.sw_loaded != -1,
                'icon-disabled': this.item.tech_plazma_count == 0, // металл не нужен
                'icon-normal':  this.item.tech_plazma_count > 0, // нужен
                'icon-good': this.item.tech_plazma_code == 2, // готово
            }
        },

        tokarka_status: function(){
            return {        			
                'hidden': this.item.sw_loaded != -1,
                'icon-disabled':  this.item.tech_tokarka_count == 0, // металл не нужен
                'icon-normal':  this.item.tech_tokarka_count > 0, // нужен
                'icon-good': this.item.tech_tokarka_code == 2, // доставлен        			
            }
        },

        sborka_status: function(){
            return {        			
                'hidden': this.item.sw_loaded != -1,
                'icon-disabled':  true, // металл не нужен
                'icon-normal':  false, // нужен
                'icon-good': false, // доставлен        			
            }
        }
    },

    methods:{
        producton_set_mark_byed_done: function(){
            // отмечаем покупные как готовые (привезенные)
            alert('Функция в разработке')
        },

        producton_set_mark_metall_done: function(){
            // отмечаем металл как готовый (привезенный)
            alert('Функция в разработке')
        }
    }
})
</script>

<style scoped>

*, ::after, ::before {
    box-sizing: unset !important
}

.item{
    position: relative;
    display: inline-block;
    width:170px;		
    height:150px;
    margin:0px;
    margin-bottom: 3px;
    padding: 0px;
    padding-left:1px;

    vertical-align: top;
    overflow: hidden;						


    background: beige;

    color:black;
    font-size:8pt;

    border-left:none;
    border-right: none;
    border-top:1px solid darkorange;
    border-bottom:1px solid  darkorange;
    border-right:1px dashed  darkorange;				
}

.ord>.item:first-child{				
    border-left:1px solid darkorange;
    border-radius:10px 0px 0px 10px;
    margin-left:0px;
    margin-right:0px;
    padding-left:0px;
}



.ord>.item:last-child{
    border-right:1px solid darkorange;
    border-radius: 0px 10px 10px 0px;
    margin-left:0px;
    margin-right:1px;
    width:169px;
}


.ord>.item:only-child{
    border-right:1px solid darkorange;
    border-left:1px solid darkorange;
    border-radius: 10px 10px 10px 10px;
    margin-left:0px;
    margin-right:1px;
    padding-left:0px;
}

.ord>.item:first-child .item_header_block_2{
    width:135px;
}


.item>*{
    margin: 0px;
    padding: 0px;
}

.isdelie_prop_container{
    padding:0px;
    margin:0px;
    height: 14px;
    border:none;
}

.isdelie_prop_name, .isdelie_prop_value{
    margin: 0px;
    padding: 0px;
    display: inline-block;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}

.isdelie_prop_name{				
    width: 40px;
    color:gray;
}

.isdelie_prop_value{
    width:120px;
}

.item_header{
    display:flex;
    flex-direction:row;
    border-bottom: 1px solid gray;
    background: lightgray;	
}

.item_header_block_1{
    color: black;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 0px;
    margin: 0px;				
    text-align: center;
    font-size: 10pt;
    display: none;
    background: darkorange;
    
}

.ord>.item:first-child .item_header_block_1{
    display: flex;
}


.order_id{
    padding: 0px;
    margin:0px;
    background: darkorange;
    border-radius:10px 0px 0px 0px;
    
    display:none;
    width: 30px;
    height: 30px;
    line-height: 22pt;
    color:black;
}


.ord>.item:first-child .order_id{
    display: block;
}


.item_header_block_2{
    color:black;
    display:flex;
    flex-direction:column;
    padding: 0px;
    padding-left:2px;
    margin:0px;	

    font-size:10pt;
    background: green;
    background: lightgray;
    
    width:170px;
    
}

.item_header_name{
    padding: 0px;
    margin:0px;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}

.item_header_describtion{
    padding: 0px;
    margin:0px;
    overflow:hidden;
    white-space:nowrap;
    text-overflow:ellipsis;
}

.icon{
    border:2px solid red;
    border-radius:5px;
    padding: 1px;
    margin:1px;
    font-size: 18pt;
    line-height: 30px;
    width:30px;
    height: 30px;
    display: inline-block;
    text-align: center;
    overflow: hidden;
}

.icon-disabled{
    color: lightgray;
    border-color: lightgray;
    fill: lightgray;
}

.icon-normal{
    color: rgb(170,70,70);
    border-color: rgb(170,70,70);
    fill: rgb(170,70,70);
}

.icon-bad{
    color: red;
    border-color: red;
    fill: red;
}

.icon-good{
    color: rgb(0,170,0);
    border-color: rgb(0,170,0);
    fill: rgb(0,170,0);
}

.icon-white{
    color: white;
    border-color: white;
    fill: white;
}


.time{
    position:absolute;
    margin-left:auto;
    margin-right: 5px;
    border-radius:5px;
    color:white;
    background: rgba(255,255,255,.1);
    
    top:1020px;
    left:1700px;
    text-align: right;
}				


.item_new{
    background: rgba(255, 0, 0, .5);
}



.category5{
    background:rgba(0,255,0,0.2);				
    color:lightgrey;
}

.category5>*{
    color:lightgrey;
    border-color:lightgrey;
}

.category4{
    background:rgba(0,0,255,0.3);
}




.pulse {	
    animation: pulse 4s infinite;
    position:absolute;
    width:100%;
    height:100%;
    background:magenta;
    z-index:19;
}
            
@keyframes pulse {
    0% {
        opacity: 0;
    }

    25% {
        opacity: .2;
    }

    50% {
        opacity: .4;
    }

    75% {
        opacity: 0.2;
    }

    100% {
        opacity: 0;
    }
}
.bottom_panel {
    position:absolute;
    background: rgba(0,0,0,.5);
    top:auto;bottom:0;
    width:100%;
    display: flex;
    color:white;
    margin:0px;
}
.inline_text{
    display: inline-block;
}
.box_posD {
    width:100%;
    height:100%;
    margin:0px;
    padding: 0px;
}
.icon_rotate {
    transform:rotate(90deg);
}
.synchro_time {
    margin-right: 10px;
    margin-left:auto;
    border-radius:5px;
    border:2px solid white;
    padding: 6px;
}
.line_dan {
    padding:0px;
    margin:0px;
    border:.5px solid black;
}
.icons_dan {
    padding:0px 2px;
    margin:0px;
    margin-top:5px;
}
</style>