<template>
    <div class="mini_card">
        <div class="mini_card_block_two">
            <div class="mini_card_block_two_box">
                <div class="mini_card_text">
                    Сотрудник
                </div>
                <div class="mini_card_inp">
                    <input class="mini_card_choice tm_input" v-model="com.com_name">
                </div>
            </div>
            <div class="mini_card_block_two_box">
                <div class="mini_card_text">
                    Дата
                </div>
                <div class="mini_card_inp">
                    <input type="date" class="mini_card_choice tm_input" v-model="com.com_date">
                </div>
            </div>
            <div class="mini_card_block_two_box">
                <div class="mini_card_text">
                    Операция
                </div>
                <div class="mini_card_inp">
                    <select disabled v-model="com.com_select" class="mini_card_choice tm_input">
                        <option value="1">Приход</option>
                        <option value="2">Уход</option>
                        <option value="3">Отпуск</option>
                    </select>
                </div>
            </div>
            <div class="mini_card_block_two_box">
                <div class="mini_card_text">
                    Время
                </div>
                <div class="mini_card_inp">
                    <input :disabled="com.com_select == 3" type="time" class="mini_card_choice tm_input" v-model="com.com_time">
                </div>
            </div>
        </div>
        <div class="mini_card_block_three">
            <button class="mini_card_block_three_btn button" @click="save">Oк</button>
            <button class="mini_card_block_three_btn button"  @click="$parent.obj.isactive = false">Отмена</button>
        </div>
        <b-loading :is-full-page="false" v-model="loading" :can-cancel="false"></b-loading>
    </div>
</template>

<script>


export default ({
    props:[ 'data'],
    data(){
        return{
            com: this.data,
            loading: false
        }  
    }, 
    computed:{
        time(){
            return this.com.com_time
        }
    
    },

    mounted(){
        this.load_time()
    },
    
    methods: {

        load_time(){
            if(this.com.com_id == null){
                this.$_send_({cmd: 'staff_get_mode_time', type:this.com.com_select, mode:'', user_id:this.com.user_id}, (response)=>{
                    this.$set(this.com, 'com_time', response.data)
                })
            }
        },

        save(){
            if(this.com.com_time == null || this.com.com_time == '') return this.$alert('Укажите время','is-warning')
            if(this.com.com_date == null || this.com.com_date == '') return this.$alert('Укажите дату','is-warning')
            this.loading = true
            let new_date
            if(this.com.com_time == null){
                new_date = this.com.com_date
            }else{
                new_date = `${this.com.com_date} ${this.com.com_time}`
            }
            if(this.com.com_id == null){
                this.$_send_({cmd:'staff_employee_turns_action_2', new_date:new_date, action: this.com.com_select, user_id: this.com.user_id}, (response)=>{
                    if(response.data.result == "-ok"){
                        this.$alert("Операция успешно завершена", "is-success")
                        // this.com.updated = !this.com.updated
                        if(this.com.open_coming){
                            this.$emit('update', {id:this.com.user_id})
                        }
                        this.$emit('update_worker', response.data.turns[0])
                        this.$parent.obj.isactive = false
                    }else{
                        this.$alert('Не удалось завершить операцию, попробуйте позже', 'is-warning')
                    }
                    this.loading = false
                })  
            }else{  
                this.$_send_({cmd:'staff_employee_edit_turn', new_date:new_date, operation_id: this.com.com_id, action:this.com.com_select, user_id: this.com.user_id}, (response)=>{
                    if(response.data.result == "-ok"){
                        this.$alert("Операция успешно завершена", "is-success")
                        // this.com.updated = !this.com.updated
                        if(this.com.open_coming){
                            this.$emit('update', {id:this.com.user_id})
                        }
                        this.$emit('update_worker', response.data.turns[0])
                        this.$parent.obj.isactive = false
                    }else{
                        this.$alert('Не удалось завершить операцию, попробуйте позже', 'is-warning')
                    }
                    this.loading = false
                }) 
            }
        },
    }   
})
</script>

<style scoped>
    button{
        cursor: pointer;
    }

    .mini_card{
        width: 350px;
        position: relative;
    }
    .mini_card_block_two{
        background: #f6f6f6;
        padding: 5px 1px;
    }
    .mini_card_block_two_box{
        display: flex;
        padding: 5px;
    }
    .mini_card_text{
        width: 30%;
        font-weight: 400;
    }
    .mini_card_inp{
        width: 70%;
    }
    .mini_card_choice{
        width: 100%;
        padding: 2px 5px;
    }
    .mini_card_block_three{
        display: flex;
        background: #f6f6f6;
        padding: 5px;
        justify-content: end;
    }
    .mini_card_block_three_btn{
        height: 25px;
        min-width: 30px;
        margin-left: 10px;
    }

</style>