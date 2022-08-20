<template>
    <div class="month-container">
        <div class="month-header">
            <b-button class="month-switcher" icon-left="angle-left" icon-pack="fas" @click.native="set_month('prev'), $emit('datepicker_change', {m:month, y:year})"></b-button>
            <b-select v-model="month" @change.native="$emit('datepicker_change', {m:month, y:year})">
                <option v-for="(month, index) in month_captions" :value="index" :key="month">
                    {{month}}
                </option>
            </b-select>
            <b-button class="month-switcher" icon-left="angle-right" icon-pack="fas" @click.native="set_month('forw'), $emit('datepicker_change', {m:month, y:year})"></b-button>
        </div>
        <div class="datepicker_content">
            <header class="datepicker_header">    
                <div class="datepicker_cell bolder" v-for="(val, index) in day_captions" v-bind:key="index">{{ val }}</div>            
            </header>
            <div class="datepicker_body">
                <div v-for="(week, index) in weeks_in_this_month" class="datepicker_row" :key="index">
                    <div v-for="(weekDay, index) in week" class="datepicker_cell"  :key="index">
                        <div  @click="$emit('datepicker_click_date', {d:weekDay, m: month, y: year})" class="day_default" :class="day_classes(weekDay)">{{weekDay.getDate()}}</div>
                        <!-- <div class="events" v-if="eventsDateMatch(weekDay)">
                            <div
                                class="event"
                                :class="event.type"
                                v-for="(event, index) in eventsDateMatch(weekDay)"
                                :key="index"/>
                        </div> -->
                    </div>
                    
                </div>
            </div>
        </div>
        <slot></slot>
    </div>
</template>

<script>
import * as my_f from "@/my"

export default {
        props:['data'],
        data: function() {
            return {                
                month_captions: ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь","Ноябрь", "Декабрь"],
                day_captions: ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"],
                days: [],
                firstDayOfWeek: 1,
                year: this.data.year,
                month: this.data.month
            }
        },
        computed: {
            month_title: function(){
                // возвращает название месяца с годом
                var tmp = this.month_captions[this.month]
                // if(this.data.show_year_in_header){
                //     tmp += ' ' + this.year
                // }
                return tmp
            },

            weeks_in_this_month() {
                const month = this.month
                const year = this.year
                const weeksInThisMonth = []

                let startingDay = 1

                while (weeksInThisMonth.length < 6) {
                    const newWeek = this.week_builder(startingDay, month, year)
                    weeksInThisMonth.push(newWeek)
                    startingDay += 7
                }
                return weeksInThisMonth
            },


         
            // current_days: function(){
            //     // возвращает массив с днями для запонения сетки
            //     // обновляется при изменении года и месяца
            //     return this.get_month_grid(this.year, this.month)
            //     return this.days
            // },

            events(){
                return this.data.events
            }
            
        },


        created(){
            // this.days = this.current_days
            // this.year = new Date().getFullYear()
            // this.month = new Date().getMonth()
        },

        methods: {
            set_month: function (par) {
                // переход на месяц назад
                switch (par) {
                    case 'prev':
                        this.month -= 1;
                        if (this.month < 0) {
                            this.month = 11;
                            this.year -= 1;
                        }
                        break;
                    case 'forw':
                        this.month += 1;
                        if (this.month > 11) {
                            this.month = 0;
                            this.year += 1;
                        }
                        break;
                
                    default:
                        break;
                }

            },

            set_month_forw: function () {
                // переход на месяц вперед
                this.data.month += 1;
                if (this.data.month > 12) {
                    this.data.month = 1;
                    this.data.year += 1;
                }
            },

            select_month_day: function (day) {
                // выбираем
                if(!this.data.selectable) return
                if (!day.current) return;
                day.select = !day.select;
            },

            get_first_day_of_month(year, month) {
                // все вычисления проводятся, исходя из нормальных номеров месяцев и дней
                // возвращает день недели, на который приходится первое число месяца
                var d = new Date(year, month, 1);
                return d.getDay();
            },

            get_length_of_month: function (year, month) {
                // возвращает длину месяца (количество дней)
                var d1 = new Date(year, month - 1, 1);
                var d2 = new Date(year, month, 1);
                return (d2 - d1) / (60 * 60 * 24 * 1000);
            },

            is_weekend: function (date) {
                // определяем, выходной ли это день
                return date.getDay() > this.data.workday_count || date.getDay() === 0;
            },

            get_month_grid: function(year, month){
                // получаем количество дней в месяце
                var month_length = this.get_length_of_month(year, month);

                //console.log(month_length)


                // получаем день недели, на который приходится первое число
                var week_day = this.get_first_day_of_month(year, month);


                switch(week_day){
                    case 0:
                        week_day=7
                        break;
                    case 1:
                        week_day=8
                }

                
                var tmp_days = []

                for (var i = 1; i <= 42; i++) {
                    // получаем порядковый номер со смещением
                    var n1 = i - week_day + 1;

                    // получаем дату
                    var date = new Date(year, month - 1, n1);
                    
                    // добавляем в массив
                    tmp_days.push({
                        index: i,
                        value: date.getDate(), // число месяца
                        current: n1 >= 1 && n1 <= month_length, // текущий месяц
                        weekend: this.is_weekend(date),
                        select: false,
                    });                
                }

                return tmp_days;
            },

            
            week_builder(startingDate, month, year) {
                const thisMonth = new Date(year, month)

                const thisWeek = []

                const dayOfWeek = new Date(year, month, startingDate).getDay()

                const end = dayOfWeek >= this.firstDayOfWeek
                    ? (dayOfWeek - this.firstDayOfWeek)
                    : ((7 - this.firstDayOfWeek) + dayOfWeek)

                let daysAgo = 1
                for (let i = 0; i < end; i++) {
                    thisWeek.unshift(new Date(
                        thisMonth.getFullYear(),
                        thisMonth.getMonth(),
                        startingDate - daysAgo)
                    )
                    daysAgo++
                }

                thisWeek.push(new Date(year, month, startingDate))

                let daysForward = 1
                while (thisWeek.length < 7) {
                    thisWeek.push(new Date(year, month, startingDate + daysForward))
                    daysForward++
                }

                return thisWeek
            },

            day_classes(day){
                let dd = new Date();
                let day_class = ''
                if(day.getMonth() != this.month){
                    day_class += " day_disabled "
                }
                if(day.getDay() == 6 || day.getDay() == 0){
                    day_class += ' day_red '
                }
                if(this. events || this.events.length > 0){
                    if(this.eventsDateMatch(day)){
                        day_class += ' has_events '
                    }
                    if(this.tm_events(day)){
                        day_class += ' day_red'
                    }
                }
                if( my_f.date_format(new Date(day), 'dd.mm.yyyy') == my_f.date_format(new Date(dd), 'dd.mm.yyyy')){
                     day_class += 'day_select'
                }
                return day_class
                // {day_disabled: !day.current, day_red: day.weekend, day_select: day.select}
            },

            eventsDateMatch(day) {
                if (!this.events || !this.events.length) return false

                const dayEvents = []

                
                return this.events.some( event =>{
                    if(event.date.getMonth() === day.getMonth()){
                        return event.date.getDate() === day.getDate()
                    }
                })
            },
            
            tm_events(day){
                if (!this.events || !this.events.length) return false
                
                return this.events.some( event =>{
                    if(event.date.getMonth() === day.getMonth()){
                        return event.date.getDate() === day.getDate() && event.weekday
                    }
                })
            }
        }
    }
</script>

<style scoped>



.month-container{
    padding: 10px 10px;
    padding-top: 0px;
}

hr{
    margin:0px;
}

.month-header{
    margin: 10px;
    display: flex;
    justify-content: space-between;    
    align-items: center;
}

>>>.month-switcher{
    color: #FF5722 ;
}

.month-title{
    width:50%;    
}

.month-switcher{
    margin:0px;
    text-align: center;
    min-width: 20px;
}

.month-grid {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    
    justify-content: space-between;
    align-content: space-between;
    width: 100%;
    padding:0px;    
    margin: auto;
}

.day,
.day_caption {    
    margin:auto;
    padding: 5px;
    flex-basis: calc(100% / 7);
    cursor: pointer;
    box-sizing: border-box;    

    text-align: right;
    
}

.day:hover:enabled {
    background: rgba(0,0,0,.2);
}

.day_caption {
 
}

.day_red {
    color: red;
}

.day_select {
    border: 2px solid #00a1ff;
}

.day_disabled {
    color: lightgray;
    pointer-events: none;
}

.datepicker_content{
    display: table;
    margin: auto;
    background: white;
    border: 1px solid lightgray;
}

.datepicker_body{
    display: table-header-group;
}

.datepicker_header{
    display: table-header-group;
    padding-bottom: 0.875rem;
    margin-bottom: 0.875rem;
    border-bottom: 1px solid hsl(0deg, 0%, 86%);
}

.datepicker_cell{
    text-align: center;
    vertical-align: middle;
    display: table-cell;
    border-radius: 4px;
    width: 55px;
    height: 50px;
}

.day_default{
    margin: 5px;
    height: 40px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    border-radius: 5px;
}

.datepicker_cell:hover:not(.datepicker_cell>.day_disabled){
    color: hsl(0deg, 0%, 4%);
    cursor: pointer;
    opacity: 0.7;
    box-shadow: rgb(0 0 0 / 16%) 0px 3px 6px, rgb(0 0 0 / 23%) 0px 3px 6px;
    transition: all 0.25s ease-in-out 0s;
}

.bolder{
    font-weight: 600;
}

.datepicker_row{
    display: table-row;
}

.has_events{
    background-color: #ff57227d;
}
</style>