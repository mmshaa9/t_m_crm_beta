<template>
    <div v-if="(user_depart == 3 || user_depart == 6 || user_depart ==  7 || user_depart ==  1)" >
        <div v-if="page == null" class="status_position">
            <div >
                Статус {{user_depart == 3 ? "проектирования" : (user_depart == 6 || user_depart == 7) ? "производства" : ''}}: <span :class="status.color">{{status.text}}</span>
            </div>
        </div>
        <div v-else  class="status_position_on_page">
            <div>
                Статус {{page.name}}: <span :class="status.color">{{status.text}}</span>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props:{
        page:{
            type: Object,
            default: null
        }
    },

    data() {
        return{
            status: {
                "Проектирование":{
                    color:'',
                    text:'',
                },
                "Производство":{
                    color:'',
                    text:'',
                }
            },
        }
    },
    computed:{

        user_depart: function(){
            return this.$store.getters.USER_DEPART
        }
    },

    

    created() {
        if(this.user_depart == 1 || this.user_depart == 3 || this.user_depart == 6 || this.user_depart == 7 || this.page != null){
            this.get_status_production()
        }
    },

    methods:{
        
        
        get_status_production(){
            let par
            if(this.user_depart == 1){
                par = "Все"
            }else{
                par = this.user_depart
            }
            if(this.page != null){
                par = this.page.id
            }
            this.$_send_({cmd: "get_status_production", par:par}, response=>{
                this.get_status_indicator(response.data)
            })
        },

        get_status_indicator(status){
            // let par
            // let par2
            // if(this.user_depart == 1){
            //     this.status = status
            //     par = this.status.Проектирование
            //     switch(true){
            //         case (par == 0):
            //             this.status.Проектирование = {text:'Отлично', color: 'my_green'}
            //             this.stats.Проектирование = 'Статус: Отлично'
            //             break
            //         case (par > 1 && par < 3):
            //             this.status.Проектирование = {text:"Хорошо", color: 'greeny'}
            //             this.stats.Проектирование = 'Статус: Хорошо'
            //             break
            //         case (par > 4 && par < 10):
            //             this.status.Проектирование = {text:"Неудовлетворительно", color: 'yellow'}
            //             this.stats.Проектирование = 'Статус: Удовлетворительно'
            //             break
            //         case (par > 10 ):
            //             this.status.Проектирование = {text: "Очень плохо", color: 'my_red'}
            //             this.stats.Проектирование = 'Статус: Очень плохо'
            //             break
            //     }
            //     par2 = this.status.Производство
            //     switch(true){
            //         case (par2 == 0):
            //             this.status.Производство =  {text:'Отлично', color: 'my_green'}
            //             this.stats.Производство = 'Статус: Отлично'
            //             break
            //         case (par2 > 1 && par < 3):
            //             this.status.Производство = {text:"Хорошо", color: 'greeny'}
            //             this.stats.Производство = 'Статус: Хорошо'
            //             break
            //         case (par2 > 4 && par < 10):
            //             this.status.Производство = {text:"Неудовлетворительно", color: 'yellow'}
            //             this.stats.Производство = 'Статус: Удовлетворительно'
            //             break
            //         case (par2 > 10 ):
            //             this.status.Производство = {text: "Очень плохо", color: 'my_red'}
            //             this.stats.Производство = 'Статус: Очень плохо'
            //             break
            //     }
            // }else{
                switch(true){
                    case (status == 0):
                        this.status = {text:'Отлично', color: 'my_green'}
                        break
                    case (status >= 1 && status <= 3):
                        this.status = {text:"Хорошо", color: 'greeny'}
                        break
                    case (status >= 4 && status <= 10):
                        this.status = {text:"Неудовлетворительно", color: 'yellow'}
                        break
                    case (status> 10 ):
                        this.status = {text: "Очень плохо", color: 'my_red'}
                        break
                }
            // }
            
        }
    }
}
</script>

<style>
    .status_position{
        /* width: 302px; */
        height: 36px;
        /* margin-left: 20px; */
        white-space: pre;
        flex-grow: 1;
    }

    .status_position_on_page{
        width: 302px;
        white-space: pre;
    }
    .greeny{
        color: rgba(114, 146, 0, 0.938)
    }

    .yellow{
        color: yellow
    }
</style>
    