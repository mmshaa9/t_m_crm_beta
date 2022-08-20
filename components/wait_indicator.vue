<template>
    <div v-show="visible" class="wait_indicator_container">
        <div class="wait_indicator_wrapper">
            <img class="wait_indicator_logo" :src="logo_url" alt="" />
            <span class="wait_indicator_message" v-html="message"></span>
        </div>
    </div>	    
</template>

<script>
/*
    компонент необходим для вывода стандартного индикатора процесса загрузки\обновления данных
    чтобы добавить его в проект:
    1. вставьте ссылку на него в объекте vue
        components: {      
            'wait-indicator': httpVueLoader('/crm/frontend/common/components/wait-indicator.vue?t=' + new Date()),
        }
    2. добавьте в data переменную  wait_indicator_visible со значением false
    3.в html-разметку вставьте строку <wait-indicator v-bind:visible="wait_indicator_visible"></wait-indicator>

*/
export default {
    props: ["visible"],
    data: function(){
        return {
            logo_url:require('@/assets/logo.png'),
            message:'Обновление<br>данных'
        }
    }
};
</script>

<style scoped>
.wait_indicator_container {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    z-index: 100;
    display: flex;
}

.wait_indicator_wrapper {
    margin: auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    animation-name: waitindicator_anamation;
    animation-duration: 2s;
    animation-iteration-count: infinite;
    animation-timing-function: ease-in-out;
}

.wait_indicator_message {
    color: white;
    text-shadow: 0px 0px 20px black;
    font-weight: bold;
    font-size: 12pt;
    text-align: center;
}

@keyframes waitindicator_anamation {
    from {
        transform: rotateY(0);
    }
    to {
        transform: rotateY(360deg);
    }
}

.wait_indicator_logo {
    filter: drop-shadow(0 0 20px black);
    width: 90px;
}
</style>
