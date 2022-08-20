<template>
    <div class="users_area">
        <div class="button_show_list mat_des_shadow" @click="get_online_users" title="Коллеги в сети"><i v-if="!small_indicator_visible1" class="fas fa-users"></i>
            <div v-if="small_indicator_visible1" class="lds-ring">
                <div></div>
                <div></div>
                <div></div>
                <div></div>
            </div>
        </div>
        <transition name="fade">
            <div class="workers_list mat_des_shadow" v-if="show_users" >
                <div v-click-outside="hide">
                    <div class="close_area"  @click="show_users = !show_users">
Коллеги в сети
</div>
                    
                    <div v-for="user in users">
                        <div class="user" @click="open_messenger(user)">
<i class="fas fa-dot-circle green"></i>{{user.Сотрудник}}
</div>
                        
                    </div>
                    <div v-if="show_all_user" v-for="all_user in all_users">
                        <div class="user" @click="open_messenger(all_user)" >
<i class="fas fa-dot-circle red"></i>{{all_user.Сотрудник}}
</div>
                    </div>
                    <div v-if="!show_all_user" @click="show_all_users" class="show_all">
Показать всех коллег
</div>
                    <div v-else @click="show_all_user = false" class="show_all">
Скрыть
</div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
import axios from 'axios'
export default ({  

    directives:{
    },

    components:{
    },
    data:function(){
        return{
            users: [],
            url: this.$store.getters.api_url_new,
            show_users: false,
            show_all_user: false,
            all_users: [],
            small_indicator_visible1: false,
            open_message: false,
            user_message: '',
            user: this.$store.getters.USER_ID,
            convs_id:'',
            positions: {
                clientX: undefined,
                clientY: undefined,
                movementX: 0,
                movementY: 0
            }
        }
    },

    created: function(){
        // console.log(this)
        // document.addEventListener('click', this.toggleDropdown.bind(this));
    },

    mounted: function(){
        // this.get_online_users()
        // setInterval(() => {
        //     this.get_online_users()
        // }, 60000*10);
    },

    methods:{

        show_list: function(){
            this.show_users = true
        },

        hide: function(){
            console.log('heh')
            this.show_users = false
        },

        get_online_users: function(){
            this.small_indicator_visible1 = true
            axios
            .get(this.url, {params: {cmd: 'get_online_users', t: new Date()}})
            .then((response) =>{
                this.show_users = true
                this.small_indicator_visible1 = false
                console.log(response.data)
                this.users = response.data.online
                this.all_users = response.data.users
            })
        },

        show_all_users: function(){
            this.show_all_user = true
        },

        open_messenger: function(user){
            this.small_indicator_visible1 = true
            axios
            .get(this.url, {params: {cmd: 'get_conversation_id', sub_user: user.Сотрудник, sub_user_id: user.Код, user: this.user, t: new Date()}})
            .then((response) =>{
                
            })
        },

        hide_messenger: function(){
            this.open_message = false
        }

    }
})
</script>

<style scoped>
.users_area{
    display: flex;
    flex-direction: column;
    align-items: center;
}

.button_show_list{
    /* border: 1px solid black; */
    margin-left: 5px;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    filter: drop-shadow(gray 0px 0px 5px);
    transition: all 0.25s ease-in-out;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}


.button_show_list:hover {
    /* background: red; */
    filter: drop-shadow(0px 0px 5px orange);
    cursor: pointer;
}

.workers_list {
    margin-top: 50px;
    width: 200px;
    min-height: 200px;
    max-height: 400px;
    background: white;
    padding: 5px;
    border-radius: 20px;
    position: absolute;
    overflow: auto;
    transition: all 0.25s ease-in-out;
}

.close_area{
    display: flex;
    justify-content: space-between;
}

.close{
    border-radius: 50%;
    background: lightgray;
    height: 30px;
    width: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    font-weight: bold;
    transition: all 0.25s ease-in-out 0s;
    /* background: white; */
}

.close:hover{
    box-shadow: rgb(0 0 0 / 12%) 0px 1px 3px, rgb(0 0 0 / 24%) 0px 1px 2px;
    filter: drop-shadow(gray 0px 0px 5px);
    cursor: pointer;
}

.fa-dot-circle {
    font-size: 14px;
}

.user {
    cursor: pointer;
}

.red {
    color: rgb(204, 15, 53);
}

.user:hover{
    border-radius: 5px;
    box-shadow: rgb(0 0 0 / 12%) 0px 1px 3px, rgb(0 0 0 / 24%) 0px 1px 2px;
}

.show_all {
    font-size: 12px;
    color: cadetblue;
}

.show_all:hover{
    cursor: pointer;
    text-decoration: underline;
}

::-webkit-scrollbar {
  width: 10px;
  opacity: 0;
}

::-webkit-scrollbar-thumb {
  border-width: 1px 1px 1px 2px;
  border-radius: 25px;
  border-color: rgb(161, 161, 161);
;
  background-color:rgb(161, 161, 161);
;
}

::-webkit-scrollbar-thumb:hover {
  border-width: 1px 1px 1px 2px;
  border-color: rgb(161, 161, 161);
  background-color: rgb(161, 161, 161);
}

::-webkit-scrollbar-track {
  border-width: 0;
}

.lds-ring {
    display: flex;
    height: 31px;
    margin-left: 5px;
    margin-top: 10px;
    position: absolute;
    padding-right: 24px;
}

.lds-ring div {
  box-sizing: border-box;
  display: flex;
  position: absolute;
  width: 20px;
  height: 20px;

  border: 2px solid #fff;
  border-radius: 50%;
  animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  border-color: orange transparent transparent transparent;
}

.lds-ring div:nth-child(1) {
  animation-delay: -0.45s;
}

.lds-ring div:nth-child(2) {
  animation-delay: -0.3s;
}

.lds-ring div:nth-child(3) {
  animation-delay: -0.15s;
}

@keyframes lds-ring {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.nedomodal {
    align-items: center;
    flex-direction: column;
    justify-content: center;
    overflow: hidden;
    /* position: fixed; */
    z-index: 40;
    bottom: 0;
    right: 0;
    
    border-radius: 4%;
    /* margin: 10px; */
}

.chat_app {
    height: 370px !important;
    width: 370px;;
}

.window {
    cursor: grab;
}
</style>