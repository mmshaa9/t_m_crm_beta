<template>
    <div class="calendar">
        <!-- <b-datepicker
            inline
            v-model="date"
            :events="turns"
            :indicators="indicators"
            :first-day-of-week="1"
            @input="set_turn"
            :multiple="false"
            size="is-large"
            @change-month=" (month) => load_turns(month)"
            @change-year="(year) => load_turns(null, year)"
            >
        <div>Количество смен: {{info.смены_количество}}</div>
        </b-datepicker> -->
        <Calendar :events="turns" @datepicker_change="load_turns($event)" @datepicker_click_date="set_turn($event)">
            <div>Количество смен: {{info.смены_количество}}</div>
        </Calendar>
    </div>
</template>

<script>
import * as my from "@/my"
import Calendar from "@/components/my-calendar-month.vue"
export default {
    data() {
        return{
            date: null,
            turns:[],
            info:[],
            bars: true,
            cur_year: null,
            cur_month: null,
            week_days:['Пн', 'Вт', 'Ср', 'Чт' ,'Пт' ,'Сб','Вс']
        }
    },

    components:{
        Calendar
    },

    computed: {
        indicators() {
            return this.bars ? 'bars' : 'dots'
        }
    },

    mounted(){
        this.cur_year = new Date().getFullYear()
        this.cur_month = new Date().getMonth()
        this.load_turns()
    },

    methods:{
        load_turns(month = null, year = null){
            this.turns = []
            if(month != null){
                this.cur_month = month
            }

            if(year != null){
                this.cur_year = year
            }
            this.$_send_({cmd:"get_staff_turns", year:this.cur_year, month:this.cur_month}, (response)=>{
                this.format_turns(response.data)
            })
        },

        format_turns(arr){
            this.info = arr[0]
            this.turns = []
            let turns = arr[0].turns
            turns.forEach(turn => {
                this.turns.push({date:new Date(turn.date), type: turn.type, weekday: turn.weekday})
            });
        },

        set_turn(date){
            // console.log(this.turns.indexOf({date:this.date, type: 'is-primary'}))
            // if(this.turns.indexOf({date:this.date, type: 'is-primary'}) != -1){
            //     this.turns.splice(this.turns.indexOf({date:this.date, type: 'is-primary'}), 1)
            // }
            if(this.turns.some( d=>{
                return my.date_format(new Date(d.date), 'dd.mm.yyyy') == my.date_format(new Date(date), 'dd.mm.yyyy')
            })){
                this.turns.splice(this.turns.indexOf(this.turns.find(d=>{
                    return my.date_format(new Date(d.date), 'dd.mm.yyyy') == my.date_format(new Date(date), 'dd.mm.yyyy')
                })), 1)
            }else{
                if(date != null){
                    this.turns.push({date:new Date(date), type: 'is-primary'})
                }
            }
            // return console.log(this.turns)
            this.$_send_({cmd:"update_turns", year:this.cur_year, month:this.cur_month, turns: JSON.stringify(this.turns)}, (response)=>{
                if(response.data.result == "-ok"){
                    this.$alert('Смены успешно обновлены', 'is-success')
                    this.format_turns(response.data)
                }else{
                    this.$alert('Не удалось обновить смены, попробуйте позже', 'is-warning')
                }
            })
        },
    }
}
</script>

<style scoped>

</style>