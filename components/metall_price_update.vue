<template>
    <div class="main_price_change">
        <div class="main_price_change_text">
            <span>Укажите среднюю самую высокую цену металлопроката за килограмм на этой неделе</span>
        </div>
        <div class="select_">
            <label>Ст3</label>
            <input class="tm_input" v-model="st3_price" type="number" placeholder="Введите цену">
        </div>
        <div class="main_price_change_buttons">
            <button class="button" @click="data.isactive = false">
Отмена
</button>
            <button class="button" @click="update_price">
Сохранить
</button>
        </div>
    </div>
</template>
    
<script>

export default ({
    props:['data'],
    data() {
        return{
            st3_price: null,
            url:''
        }
    },

    methods:{
        update_price(){
            this.$_send_({cmd:'metall_update_average_price', price: this.st3_price}, (response)=>{
                if(response.data.result == "-ok"){
                    this.$alert('Цена успешно обновлена', 'is-success')
                    this.data.avg_price = response.data.new_price
                    this.data.isactive = false
                }else{
                    this.$alert('Не удалось обновить цену, попробуйте позже', 'is-warning')
                }
            })
            // var bodyFormData = new FormData()
            // bodyFormData.append('cmd', 'metall_update_average_price')
            // bodyFormData.append('price', this.st3_price)

            
            // axios({
            //         method: 'post',
            //         url: app.$store.getters.api_url_new,
            //         data: bodyFormData,
            //         headers: {'Content-Type': 'multipart/form-data' },
            //         }) 
            // .then((response)=>{
                
            //     console.log(response.data)
            // })
        }
    }
})
</script>

<style scoped>
    .main_price_change{
        width: 300px;
        height: 200px;
        padding: 5px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
    }

    .main_price_change_text {
        margin-bottom: 10px;
        border: 1px solid gray;
        padding: 5px;
        font-size: 14px;
    }
    .main_price_change_buttons{
        display: flex;
        justify-content: flex-end;
    }

    .main_price_change_buttons>*{
        margin: 0px;
        height: 30px;
    }

    .select_{
        outline: none;
        display: flex;
        align-items: center;
        justify-content: space-between;
        word-break: break-all;
    }

    .select_>input, select, label{
        width: 25%;
    }
</style>
