<template>
    <div class="regime_page">
        <div class="head_staff">
            <div class="regime_butt_nev">
                <button class="regime_btn_nev button" @click="open_n_regim">Добавить режим</button>
            </div>
        </div>
        <div class="regime_box">
            <div class="regime_list" v-for="(mode, index) in modes" :key="mode.Код">
                <div  class="regime_content">
                    <div class="regime_numer">{{index+1}}</div>
                    <div class="regime_text">{{mode.name}}</div>
                </div>
                <div class="regime_butt">
                    <button class="regime_del_btn" @click="confirm_delete_mode(mode)">Удалить</button>
                </div>
            </div> 
        </div> 
        <window :obj="n_regim" :prop="n_regim" :component="n_regim.n_regim" @update_list="load_data" @close="close_n_regim" :cur_id="n_regim.id"></window>        
    </div>
</template>

<script>
import my_window  from "@/components/window.vue"
import new_regime  from "@/components/staff_new_regime.vue"
import { mapActions } from 'vuex'
    export default ({
        props:['data'],
        components:{
            'window': my_window,
        },

        data(){
            return {
                modes: this.data,
                n_regim:{
                    isactive: false,
                    header_modal: 'Добавить новый режим',
                    id: null,
                    n_regim: new_regime,
                    updated: false
                },
            }  
        },

        methods:{
            ...mapActions([
                'show_alert',
                'confirm'
            ]),
            load_data(){
                this.$_send_({cmd: "staff_get_org_employees_digest"}, (response)=>{
                    this.modes = response.data.modes 
                })
            },

            open_n_regim(){
                this.n_regim.isactive= true 
            },

            close_n_regim(){
                this.n_regim.isactive=false
            },

            confirm_delete_mode(mode){
                this.confirm({
                    text:  "Вы действительно хотите удалить режим? Результаты вычислений за предыдущие месяцы могут измениться.",
                    confirm_action:()=>{this.delete_mode(mode)}
                })
            },

            delete_mode(mode){
                this.$_send_({cmd: 'staff_mode_delete', id: mode.Код}, response=>{
                    if(response.data.result == "-ok"){
                        this.$alert("Режим успешно удален", 'is-success')
                        this.modes = response.data.modes
                    }else{
                        this.$alert("не удалось удалить режим, попробуйте позже", 'is-success')
                    }
                })
            }
        }   
    })
</script>

<style scoped>
    button{
        cursor: pointer;
    }
    .regime_page {
        width: 1000px;
        background: white;
        min-height: 500px;
        height:  620px;
    }
    .head_staff {
        display: flex;
        justify-content: center;
        background: rgb(225, 225, 225);
        border-top: 1px solid gray;
        border-bottom: 1px solid gray;
        padding: 5px 10px;
    }
    .regime_btn_nev {
        height: 30px;
    }
    .regime_list {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 10px;
    }
    .regime_content{
        display: flex;
    }
    .regime_numer {
        font-weight: 600;
        padding-right: 20px;
    }
    .regime_del_btn {
        border: none;
        background: local;
        font-weight: 600;
    }
    
    .regime_box{
        overflow: auto;
        height: 575px;
    }


</style>