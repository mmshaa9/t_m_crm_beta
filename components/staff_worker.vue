<template>
    <!-- Обычный сотрудник -->
    <div class="inner_inner_list" >
        <div class="main_list_inner" v-if="worker.depart != '6' & worker.depart != '7' ">
            <div class="list_content">
                <div class="img list_item_two list_title">
                    <div v-if="worker.photo" class="img_list_worker">
                        <img :src="worker.photo" class="worker_photo_src">
                    </div>
                    <div v-else  class="img_list_worker no_photo">
                        <i  class="far fa-user worker_photo_src"></i>
                    </div>
                </div>
                <div class="list_title list_item_one">
                    <slot></slot>
                </div>
                <div class="list_title list_item_three">
                    <div class="staff_text_name name_click" @click="$emit('open_worker_card', worker)">{{worker.name}}</div>
                    <div class="staff_butt">
                        <div>
                            <button title="Приход-Уход" class="staff_btn name_click" @click="$emit('open_coming' , worker)"><i class="fas fa-exchange-alt color1"></i></button>
                        </div>
                        <div>
                            <button title="Пропуск" class="staff_btn name_click" @click="$emit('open_pass' ,worker)"><i class="far fa-address-card color2"></i></button>
                        </div>
                    </div>
                </div>
                <div class="list_item">
                    <div class="list_title list_item_four">{{worker.salary}}</div>   
                    <div class="list_title list_item_five">{{worker.mode}}</div>
                    <div class="list_title list_item_six">{{worker.phone}}</div>
                    <div class="list_title list_item_seven">{{worker.mail}}</div>
                </div>
            </div>
        </div>
        <!-- Производство -->
        <div class="main_list_inner" v-else>
            <div class="list_content">
                <div class=" img list_item_two list_title">
                    <div v-if="worker.photo" class="img_list_worker">
                        <img  :src="worker.photo" class="worker_photo_src">
                    </div>
                    <div v-else  class="img_list_worker no_photo">
                        <i  class="far fa-user worker_photo_src" ></i>
                    </div>
                </div>
                <div class="list_title list_item_one">
                    <slot></slot>
                </div>
                <div class="list_title list_item_three">
                    <div class="staff_text_name name_click" @click="$emit('open_worker_card', worker)">{{worker.name}}</div>
                    <div class="staff_butt">
                        <div>
                            <button title="Приход-Уход" class="staff_btn name_click" @click="$emit('open_coming' , worker)"><i class="fas fa-exchange-alt color1"></i></button>
                        </div>
                        <div>
                            <button title="Пропуск" class="staff_btn name_click" @click="$emit('open_pass' , worker)"><i class="far fa-address-card color2"></i></button>
                        </div>
                        <div>
                            <button title="Отработанные смены" class="staff_btn name_click"  @click="$emit('open_cal' , worker)"><i class="far fa-calendar-alt color4"></i></button>
                        </div>
                    </div>
                    <div class="worker_graph_summary" v-if="conceal.graph.visible">
                        {{isNaN(worker.hours_count) ? null : worker.hours_count*1}}
                    </div>
                </div>
                <div class="list_item list_item_2" v-if="!conceal.graph.visible">
                    <div title="Измениить оклад" class="name_click" @click="$emit('open_imp_box' , worker )">{{worker.salary ?? null}}</div>
                    <div title="Выбрать режим" class="name_click" @click="change_mode">{{worker.mode_name ?? worker.mode}}</div>
                    <div class="">{{isNaN(worker.turns_count) ? null : worker.turns_count*1}}</div>
                    <div class="">{{isNaN(worker.hours_for_salary) ? null : worker.hours_for_salary*1}}</div>
                    <div class="">{{isNaN(worker.hours_count) ? null : worker.hours_count*1}}</div>
                    <div class="">{{isNaN(worker.overworking) ? null : worker.overworking*1}}</div>
                    <div class="">{{worker.time_price ?? null}}</div>
                    <div class="">{{worker.bounty ?? null}}</div>
                    <div class="">{{worker.summary ?? null}}</div>
                </div>
                <div class="list_item_graf" v-else>
                    <!-- <div class="list_title list_item_6">{{isNaN(worker.hours_count) ? null : worker.hours_count*1}}</div> -->
                    <div v-for="(day, index) in days" >
                        <div class="item_day" :class="{day_color: typeof worker.month_turns != 'undefined' ? (worker.month_turns[day.id] > 12) : false, today_color: is_today(day.id), weekends_color: day.weekend}" @click="get_options(day.id)">{{typeof worker.month_turns != 'undefined' ? (worker.month_turns[day.id] == null ? null : worker.month_turns[day.id]*1) : null}}</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

import {mapActions} from "vuex";
import * as my_f from "@/my"

    export default ({
        props:['conceal', 'employee', 'dm', 'date', 'options', 'modes'],
        data(){
            return {
                list_active: undefined,
                // employ: this.employee
            }  
        },  

        computed:{
            worker(){
                return this.employee
            },

            days(){
                return this.dm
            },

            month(){
                return this.date.month
            },

            year(){
                return this.date.year
            },
        },

        created(){
            this.$set(this.worker, 'graf', {visible: false})
            // this.load_data()
        },
        
        mounted(){
            
        },

        methods:{
            // load_data(){
            //     this.$_send_({cmd:'staff_get_org_employees_digest'}, (response)=>{
            //         this.modes = response.data.modes
            //     })
            // },
            open_coming(){
                this.$_send_({cmd: "staff_get_employee_turns", user_id: this.worker.id}, (response)=>{
                    this.$set(this.worker, 'turns', response.data)
                    this.coming.isactive = !this.coming.isactive
                })
            },

            close_coming(){
                this.coming.isactive = false
            },

            ...mapActions([
                'select'
            ]),
        
            get_options(day){
                this.select({
                    items:this.options.items,

                    set_action: (option)=>{
                        this.turns_action(option, day)
                    },

                    select_option: (option) =>{

                    }
                })
            },
            
            turns_action(option, day){
                if(option.id == 4 || option.id == 5){
                    this.$emit('open_mini_coming', {
                        id:this.worker.id,
                        name: this.worker.name,
                        date: my_f.date_format(new Date(`${this.year}-${(this.month+1)}-${day}`), 'yyyy-mm-dd'),
                        i: (option.id-3),
                        open_coming: false,
                        worker: this.worker
                    })
                    return
                }

                this.$emit('update:indicator', true)
                
                this.$_send_({cmd:'staff_employee_turns_action', day:day, month: this.month, year: this.year, depart_id: this.worker.depart, action: option.id, user_id: this.worker.id}, (response)=>{
                    this.$emit('update:indicator', false)
                    if(response.data.result == "-ok"){
                        this.$alert("Операция успешно завершена", "is-success")
                        this.$emit('update_worker', response.data.turns[0])
                    }else{
                        this.$alert('Не удалось завершить операцию, попробуйте позже', 'is-warning')
                    }
                })  
            },

            is_today(date){
                date = new Date(this.year, this.month, date);
                const today = new Date()
                return date.getDate() == today.getDate() &&
                    date.getMonth() == today.getMonth() &&
                    date.getFullYear() == today.getFullYear()
            },
            change_mode(){
                this.select({
                    items:this.modes,
                    set_action: (option)=>{
                        this.set_mode(option)
                    },
                    select_option: (option) =>{
                    }
                })
            },
            set_mode(option){
                this.$emit('update:indicator', true)
                this.$_send_({cmd:'staff_employee_mode_salary_actions', user_id:this.worker.id, year:this.year, month:this.month, salary: this.worker.salary, mode: option.Код}, (response)=>{
                    this.$emit('update:indicator', false)
                    if(response.data.result == "-ok"){
                        this.$alert("Операция успешно завершена", "is-success")
                        this.$emit('update_worker', response.data.info[0])
                    }else{
                        this.$alert('Не удалось завершить операцию, попробуйте позже', 'is-warning')
                    }
                })
            },
        }
    })
</script>

<style scoped>
    .inner_inner_list {
        margin: 0px;
        height: 100%;
        display: flex;
        flex-direction: column;
    }

    /* .main_list_inner:hover{
        background: #ececec;
    } */
    .main_list_inner {
        background: #ffffff;
        border-width: 0px 1px 1px 1px;
    }
    .list_content {
        display: flex;
        align-items: center;
        font-weight: 400;
        border: 1px solid lightgray;
        border-top: none;

    }
    .list_item{
        display: flex;
        width: 60%;
        margin-right: 5px;
    }

    .list_item_2>*{
        width: 150px;
        padding-left: 5px;
    }

    .list_item_2>*:nth-child(n+3){
        width: 110px;
    }
    
     .list_item_one {
        width: 2.5%;
        min-width: 2.5%;
    }
    .list_item_three {
        width: 32.5%;
        display: flex;
        flex-grow: 1;
    }
    .list_item_four {
        width: 20%;
    }
    .list_item_five {
        width: 20%;
    }
    .list_item_six {
        width: 20%;
    }
    .list_item_seven {
        width: 25%;
    }
    .list_item_4{
        width: 17%;
    }
    .list_item_5{
        width: 15%;
    }
    .list_item_6{
        width: 10%;
    }
    .list_item_7{
        width: 10%;
    }
    .list_item_8{
        width: 10%;
    }
    .list_item_9{
        width: 15%;
    }
    .list_item_10{
        width: 15%;
    }
    .list_item_11{
        width: 13%;
    }
    .list_item_12{
        width: 10%;
    }
    .list_item_graf{
        display: flex;
        justify-content: space-between;
        width: 60%;
        margin-right: 5px;
    }

    .item_day {
        background: #e7e7e7;
        padding: 1px 0px;
        cursor: pointer;
        width: 30px;
        height: 38px;
        margin-right: 1px;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .item_day:hover{
        /* background-color: hsl(0deg, 0%, 96%);
        color: hsl(0deg, 0%, 4%); */
        opacity: 0.65;
        cursor: pointer;
    }

    .staff_butt {
        display: flex;
        align-items: center;
        margin-left: 20px;
    }
    .staff_btn {
        background: none;
        border: none;
        font-size: 18px;
    }
    button{
        cursor: pointer;
    }
    .staff_text_name{
        width: 55%;
    }
    .name_click{
        cursor: pointer;
    }
    .name_click:hover{
        text-shadow: 1px 1px 7px orange;
    }
    .fa-user,
    .fa-file-invoice-dollar,
    .fa-industry,
    .fa-building{
        font-size: 35px;
        opacity: 0.89;
    }
    .color1{
        color: #00aa2d;
    }
    .color2{
        color: #786260;
    }
    .color3{
      color: #039BE5;  
    }
    .color4{
        color: #9677d2;
    }
    .day_color{
        color: pink
    }
    .today_color{
        background: #00a1ff52;
    }

    .weekends_color{
        background: pink;
    }

    .img_list_worker {
        display: flex;
        height: 50px;
        padding: 5px;
        width: 50px;
        justify-content: center;
    }

    .no_photo{
        /* margin-left: 5px; */
    }

    .worker_photo_src{
        border-radius: 10px;
        width: 100%;
        border: 1px solid lightgray;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 20px;
    }

    .list_title{
        padding-left: 5px;
    }
    .list_item_two {
        width: 3%;
        padding: 0px;
    }

    .worker_graph_summary{
        flex-grow: 1;
        display: flex;
        justify-content: center;
    }
</style>

