<template>
    <div>
        <div class="main mat_des_shadow">
            <img id="image" class="profile_img" :title="info.Сотрудник" :src="photo" @click="open_info">
        </div>
        <b-modal v-model="open" :width="400">
            <div class="modal_card">
                <div>Сотрудник</div>
                <div class="card">
                    <div class="info">
                        <div :key='1'>
                            ФИО: {{info.фио}}
                        </div>
                        <div>Телефон: {{info.Телефон}}</div>
                        <div>Почта: {{info.Почта}}</div>
                    </div>
                    <!-- <div v-if="user_depart == 2">
                        Обновить статус
                    </div> -->
                    
                    <!-- <div v-else class="info">
                        <div>ФИО: {{info.фио}}</div>
                        <b-input placeholder="Телефон" size="is-small" >Телефон: {{info.Телефон}}</b-input>
                        <b-input placeholder="Почта" size="is-small">Почта: {{info.Почта}}</b-input>
                    </div>  -->
                </div>
                <!-- <b-button type="is-info" size="is-small" @click="edit=!edit">Изменить информацию</b-button> -->
            </div>
        </b-modal>
    </div>
</template>

<script>
import axios from 'axios'

export default ({
    data(){
        return {
            user: this.$store.getters.USER_ID,
            user_depart: this.$store.getters.USER_DEPART,
            url: this.$store.getters.api_url_new,
            photo: null,
            info: '',
            open: false,
            edit: false,

        }
    },

    mounted(){
        this.get_user_profile()
    },

    methods: {

        open_info(){
            this.open = true

        },

        get_user_profile(){
            axios
                .get(this.url, {params: {cmd:'get_user_profile', user: this.user, t: new Date()}})
                .then((response) =>{   
                    this.info = response.data[0]
                    if(!this.info.фото){
                        this.photo = require("@/assets/user.png")
                    }else{
                        this.photo = this.info.фото
                    }
                })
        },

        get_user_photo(){
            axios
                .get(this.url, {params: {cmd:'get_user_photo', user: this.user, t: new Date()}, responseType: 'blob'})
                .then((response) =>{  
                    // var textPromise = response.data.text();
                    // textPromise.then(text => {
                    //     if(text == ""){
                            
                    //         document.querySelector("#image").src = require("@/assets/user.png")
                    //     }else{
                    //         var urlCreator = window.URL || window.webkitURL;
                    //         var imageUrl = urlCreator.createObjectURL(response.data);
                    //         document.querySelector("#image").src = imageUrl;
                    //     }
                    // })
                    if(!response.data){
                        this.photo = require("@/assets/user.png")
                    }else{
                        this.photo = response.data
                    }
                })
        },

        edit_info(){

        }

        
    }
})

</script>

<style scoped>
    .main {
        display: flex;
        border-radius: 50%;
        width: 40px;
        height: 40px;
        margin-left: 5px;
        transition: all 0.25s ease-in-out 0s;
    }
    .profile_img {
        border-radius: 50%;
        cursor: pointer;
        max-height: unset;
        height: 42px;
        width: 42px;
        object-fit: cover;
    }


    .main:hover {
        /* background: red; */
        filter: drop-shadow(0px 0px 5px orange);
    }

    img {
        /* filter: drop-shadow(0px 0px 5px gray); */
    }

    .modal_card {
        background-color: #ececec ;
        padding: 10px;
        border: 1px solid black;
        border-radius: 10px;
    }

    .info {
        background: white;
        padding: 5px;
        box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
        transition: all 0.25s ease-in-out;
    }

    .fade-enter-active,
    .fade-leave-active {
    transition-property: opacity;
    transition-duration: 0.25s;
    }

    .fade-enter-active {
    transition-delay: 0.25s;
    }

    .fade-enter,
    .fade-leave-active {
    opacity: 0;
    }
</style>
