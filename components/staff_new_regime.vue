<template>
    <div class="new_regime">
        <div class="new_regime_content">
            <div class="new_regime_line">
                <div class="new_regime_line_text">Название</div>
                <div class="new_regime_line_inp">
                    <input type="text" required class="new_regime_line_inp_text tm_input" v-model="regim.name">
                </div>
            </div>
            <div class="new_regime_line">
                <div class="new_regime_line_text">
                    Дни
                </div>
                <div class="new_regime_line_inp">
                    <div class="new_regime_days" v-for="(day, key) in regim.days" :key="key">
                        <button class="new_regime_day" :class="day.active" @click="day_chk(day, key)" >{{day.name}}</button>
                    </div>
                </div>
            </div>
            <div class="new_regime_line">
                <div class="new_regime_line_text">Начало, ч:м</div>
                <div class="new_regime_line_inp">
                    <input type="time" required class="new_regime_line_inp_text tm_input" v-model="regim.begin">
                </div>
            </div>
            <div class="new_regime_line">
                <div class="new_regime_line_text">Конец, ч:м</div>
                <div class="new_regime_line_inp">
                    <input type="time" required class="new_regime_line_inp_text tm_input" v-model="regim.end">
                </div>
            </div>
            <div class="new_regime_line">
                <div class="new_regime_line_text">Обед, ч</div>
                <div class="new_regime_line_inp">
                    <input type="number" class="new_regime_line_inp_text tm_input" v-model="regim.lunch">
                </div>
            </div>
            <div class="new_regime_line">
                <div class="new_regime_line_text">Переработка</div>
                <div class="new_regime_line_inp">
                    <b-switch v-model="shipped"
                        :left-label='true'
                        size="is-small"
                        class="top_switch"
                        @change.native="overtime_chk">
                    </b-switch> 
                </div>
            </div>
            <hr>
            <div class="new_regime_line">
                <div class="new_regime_line_text">День, часы</div>
                <div class="new_regime_line_inp">
                    <input type="text" class="new_regime_line_inp_text tm_input" disabled v-model="day_hours">
                </div>
            </div>
            <div class="new_regime_line">
                <div class="new_regime_line_text">Неделя, часы</div>
                <div class="new_regime_line_inp">
                    <input type="text" class="new_regime_line_inp_text tm_input" disabled v-model="week_hours">
                </div>
            </div>
            <div class="new_regime_line">
                <div class="new_regime_butt">
                    <button class="new_regime_btn button" @click="save">Сохранить</button>
                    <button class="new_regime_btn button" @click="$parent.obj.isactive = false">Отмена</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

    export default ({
        props:['data'],
        data(){
            return {
               regim:{
                   name: undefined,
                   days:[
                       {
                           name: 'Пн',
                           check: false,
                           active: undefined,
                       } ,
                       {
                           name: 'Вт',
                           check: false,
                           active: undefined,
                       } ,
                       {
                           name: 'Ср',
                           check: false,
                           active: undefined,
                       } ,
                       {
                           name: 'Чт',
                           check: false,
                           active: undefined,
                       } ,
                       {
                           name: 'Пт',
                           check: false,
                           active: undefined,
                       } ,
                       {
                           name: 'Сб',
                           check: false,
                           active: undefined,
                       } ,
                       {
                           name: 'Вс',
                           check: false,
                           active: undefined,
                       } ,
                   ],
                   begin: '',
                   end: '',
                   lunch: null,
                   overtime: false,

               },
               active: undefined,
               shipped: false,
            }  
        },

        computed:{
            day_hours(){
                let begin = this.regim.begin.split(":")
                let end = this.regim.end.split(":")
                let res = (new Date(new Date(2020, 1,1, end[0], end[1]) - new Date(2020, 1,1, begin[0], begin[1])))
                res = Math.floor(res / 1000 / 60 / 60) - this.regim.lunch
                if(isNaN(res)){
                    return null
                }
                return res;
            },

            week_hours(){
                let weeks = this.regim.days.filter( day=> day.check)
                if(isNaN(this.day_hours)){
                    return null
                }
                return this.day_hours * weeks.length
            }
        },

        methods:{
            day_chk(day, key){
                day.check=!day.check
                if(day.check){
                    day.active = 'active'
                }else{
                    day.active = ''
                }
            },
            overtime_chk(){
                this.regim.overtime =! this.regim.overtime
            },

            save(){
                if(this.regim.name == undefined || this.regim.name == null || this.regim.name == "" ) return this.$alert('Укажите название режима', 'is-warning')
                if(this.regim.begin == undefined || this.regim.begin == null || this.regim.begin == "" ) return this.$alert('Укажите начало режима', 'is-warning')
                if(this.regim.end == undefined || this.regim.end == null || this.regim.end == "" ) return this.$alert('Укажите конец режима', 'is-warning')
                if(this.regim.lunch == undefined || this.regim.lunch == null || this.regim.lunch == "" ) return this.$alert('Укажите количество часов на обед', 'is-warning')
                this.$_send_({cmd:"staff_add_new_mode", mode: JSON.stringify(this.regim)}, (response)=>{
                    if(response.data.result == '-ok'){
                        this.$alert("Режим успешно добавлен", 'is-success')
                        this.$parent.obj.isactive = false
                        this.$emit('update_list')
                    }else{
                        this.$alert("Не удалось добавить режим, попробуйте похже", 'is-success')
                    }
                })  
            }
        }
    })
</script>


<style scoped>
    .active{
        background: lightgray;
        box-shadow: 0px 1px 3px rgb(0 0 0 / 30%);
    }
    button{
        cursor: pointer;
    }
    .new_regime{
        width: 350px;
        background: rgb(246, 246, 246);
    }
    .new_regime_line{
        display: flex;
        justify-content: flex-end;
        padding: 8px;
    }
    .new_regime_day{
        border: 1px solid gray;
        padding: 5px;
        color: rgb(67, 67, 67);
        border-radius: 3px;
    }
    .new_regime_line_text{
        width: 40%;
    }
    .new_regime_line_inp{
        width: 80%;
        display: flex;
        justify-content: space-between;
    }
    .new_regime_line_inp_text{
        width: 100%;
        height: 25px;
    }
    hr{
        background-color: gainsboro;
        margin: 0 5px;
    }
    .new_regime_btn{
        margin-left: 10px;
        height: 25px;
        max-width: 30px;
    }
    .top_switch{
        font-size: 14px;
        /* padding-left: 10px; */
    }
</style>