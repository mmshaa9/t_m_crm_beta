<template>
    <!--------Кнопка Приход Уход -->
    <div class="coming_box">
        <div class="coming_page">
            <div class="coming_block_two">
                <div class="block_two_butt">
                    <button class="block_two_btn1 button" @click="$emit('open_mini_coming', {turn: null, name: data.name2, id: data.id, i:1, worker: data})">Приход</button>
                </div>
                <div class="block_two_butt">
                    <button class="block_two_btn1 button" @click="$emit('open_mini_coming', {turn: null, name: data.name2, id: data.id, i:2, worker: data})">Уход</button>
                </div>
                <div class="block_two_butt">
                    <button class="block_two_btn1 button" @click="$emit('open_mini_coming' , {turn: null, name: data.name2, id: data.id, i:3, worker: data})">Отпуск</button>
                </div>
            </div>
            <div class="coming_block_three">
                <div class="block_three_content" v-for="turn in data.turns" :key="turn.Код">
                    <div class="content_block_one">
                        <div class="content_block_one_inpt bold">{{turn.Заголовок}}</div>
                    </div>
                    <div class="content_block_two">
                        <div class="content_block_two_inf bord">
                            <div class="content_block_two_inpt">{{turn.ПришелВремя}}</div>
                            <button class="content_block_two_btn" @click="$emit('open_mini_coming',  {turn: turn, name: data.name2, id: data.id, i:1})">...</button>
                        </div>
                        <div class="content_block_two_inf">
                            <div class="content_block_two_inpt">{{turn.УшелВремя}}</div>
                            <button class="content_block_two_btn" @click="$emit('open_mini_coming' , {turn: turn, name: data.name2, id: data.id, i:2})">...</button>
                        </div>
                    </div>
                    <div class="content_block_three">
                        <div class="content_block_three_box box_one"><img :src="turn.Фото1"></div>
                        <div class="content_block_three_box"><img :src="turn.Фото2"></div>
                    </div>
                    <div class="content_block_four">
                        <button class="content_block_four_btn" @click="$emit('delete_turn', turn)">Удалить смену</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default ({
        props:[ 'data'],
        data(){
            return {
            }  
        }, 
        computed:{
        
        },
        methods: {
            action(){
                var bodyFormData = new FormData()
                bodyFormData.append('cmd', '')
                axios({
                        method: 'post',
                        url: this.url,
                        data: bodyFormData,
                        headers: {'Content-Type': 'multipart/form-data' },
                        }) 
                .then((response)=>{
                    console.log(response.data)
                })
            },
           
        }   
    })
</script>

<style scoped>

    .coming_box{
        width: 600px;
        height: 400px;
        background-color: #ffffff;
        
    }
    .coming_page{
        height: 350px;
        width: 100%;
        position: absolute;
    }
    .coming_block_two{
        display: flex;
        justify-content: space-around;
        padding: 5px 0;
        background: #f6f6f6;
        border-bottom: 1px solid lightgray;
        border-top: 1px solid lightgray;
    }
    .block_two_btn1{
        height: 30px;
    }
    .block_two_btn{
        padding: 2px 10px;
        color: #4f4f4f;
        cursor: pointer;
        border: 1px solid lightgray;
    }
    .content_block_one{
        border-bottom: 1px solid lightgray;
    }
    .content_block_one_inpt{
        width: 100%;
        padding: 3px 0;
        background: #f6f6f6;
        border: none;
        text-align: center
    }
    .content_block_two{
        display: flex;
        border-bottom: 1px solid lightgray;
    }
    .content_block_two_inf{
        width: 50%;
        display: flex;
        justify-content: space-between;
    }
    .bord{
         border-right: 1px solid lightgray;
    }
    .content_block_two_inpt{
        width: 89%;
        border: none;
        padding: 5px;
        text-align: center
    }
    .content_block_two_btn{
        border: none;
        border-left: 1px solid lightgray;
        padding: 7px 10px;
    }
    .coming_block_three{
        overflow: auto ;
        height: 100%;
    }
    .block_three_content{
        margin: 10px 20px;
        border: 1px solid lightgray;
    }
    .content_block_three{
        display: flex;
    }
    .content_block_three_box{
        min-height: 150px;
        width: 50%;
        border-bottom: 1px solid lightgray;
    }
    .box_one{
        border-right: 1px solid lightgray;
    }
    .content_block_four_btn{
        width: 100%;
        border: none;
        padding: 5px 0;
        color: #4f4f4f;
        background: none;
        font-size: 12px;
        font-weight: bolder;
    }
    
</style>