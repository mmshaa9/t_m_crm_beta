<template>
    <!-- <div> -->
        <!-- <transition name="slide-in"> -->
            <div class="chat_side" v-if="hidden_chat">
                <i @click="$emit('hide_chat')" class="fas fa-comment-alt tab_chat_visible" title="Скрыть чат"></i>
                <chat class="mat_des_shadow chat_border_rad" :class="{disabled: convers_id == 0 || convers_id == '' || convers_id == '0'}" :convs_id="convers_id" :user="cu" :key="chatkey" ></chat>
                
            </div>
            
            <div v-else class="chat_side_hiddens">
                <!-- <transition name="fade"> -->
                    <i  @click="$emit('hide_chat')" class="fas fa-comment-alt tab_chat mat_des_shadow" title="Открыть чат"></i>
                <!-- </transition> -->
            </div>
        <!-- </transition> -->
    <!-- </div> -->
</template>

<script>
// Компонент позволяет встроить в страницу скрывающийся чат. На вход принимает код беседы, код юзера, а так же переменную скрытия чата
// В родительском компоненте необходимо наличие функции "hide_chat"
// 
import main_chat from "@/components/main_chat.vue"

export default {

    components: {
        'chat': main_chat
    },
    props:{
        cu:{
            type: String,
            required: true
        }, 
        convers_id:{
            type: String,
            required: true
        },
        hidden_chat:{
            type: Boolean,
            required: true
        },
        chatkey:{
            type: Number,
            required: true
        }
    },
    data(){
        return{
            hidden_chatik: this.hidden_chat,
        }
    },

    created(){
        this.$parent.$on('open_chat', this.open_chat)
    },

    methods:{
        open_chat: function(){
            this.hidden_chatik = !this.hidden_chatik
        }
    },
}
</script>

<style scoped>


.chat_app{
    width: 100%;
}
.chat_side {
    padding: 1px;
    /* background: lightgray; */
    display: flex;
    padding-top: 4px;
    width: 25%;
    transition: all 0.25s ease-in-out;
}

.chat_side_hiddens {
    padding: 1px;
    display: flex;
    padding-top: 4px;
    transition: all 0.25s ease-in-out;
    /* background: red; */
}


.tab_chat {
    width: 25px;
    height: 50px;
    background: rgb(239, 239, 209);
    border: 1px solid lightgray;
    position: absolute;
    right: 0;
    border-radius: 5px;
    text-align: center;
    top: 100px;
    padding-top: 15px;
    padding-left: 3px;
    padding-right: 3px;
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    cursor: pointer;
}

.tab_chat:hover {
    /* opacity: 0.9 */
    width:50px;
    transition: all 0.3s ease-in-out;
}

.tab_chat_visible {
    width: 25px;
    height: 50px;
    background: rgb(239, 239, 209);
    border: 1px solid lightgray;
    border-radius: 5px;
    text-align: center;
    padding-top: 15px;
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    margin-top: 35px;
    box-shadow: rgb(0 0 0 / 16%) 0px 0px 0px, rgb(0 0 0 / 23%) -4px 1px 6px;
    padding-left: 3px;
    z-index: 20;
    border-right: unset;
    padding-right: 3px;
    cursor: pointer;
}


.chat_border_rad {
    border-radius: 10px;
}
</style>