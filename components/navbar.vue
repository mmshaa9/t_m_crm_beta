<template>
    <b-navbar class="topped"  >
        <!-- <template #brand >
            <b-navbar-item tag="router-link" :to="{ path: '/' }">
                <img
                    src="favicon.ico"
                    alt="TulMash"
                >
            </b-navbar-item>
        </template> -->
        <template #start>
            <div v-if="Object.keys(pages).length > 1" class="departs_">
            <!-- <div class="indicator">{{status[page.label]}}</div> -->
                <b-navbar-dropdown :label="page.label" v-for='(page,index) in pages' :key="index" :title="stats[page.label]" :hoverable="true" collapsible>
                    <b-navbar-item v-for="(item, index) of page.items" tag="router-link" v-if="!item.hidden"  :to="{name: item.path}" :key="index">{{item.name}}
                    </b-navbar-item>
                </b-navbar-dropdown>
            </div>
            <div v-else v-for='(page,index) in pages' :key="index" class="departs_">
                <span  class="departs_" v-if="pages_count > 12">
                    <b-navbar-item @click.native="set_page(item)" :class="{active_list: active == item.name}" v-for="(item, index) of page.items.slice(0, 12)" v-if="!item.hidden"  class="active_page"    :key="index">{{item.name}}</b-navbar-item>
                    <b-navbar-dropdown class="hidden_pages" collapsible>
                        <b-navbar-item  v-for="(item, index) of page.items.slice(12)" v-if="!item.hidden"  tag="router-link" :to="{name: item.path}" :key="index">{{item.name}}
                        </b-navbar-item>
                    </b-navbar-dropdown>
                </span>
                <span  class="departs_" v-else>
                    <b-navbar-item @click.native="set_page(item)" :class="{active_list: active == item.name}" v-for="(item, index) of page.items" class="active_page"  v-if="!item.hidden"   :key="index">{{item.name}}</b-navbar-item>
                </span>
                <!-- <b-navbar-item @click.native="set_page(item)" :class="{active_list: active == item.name}" v-for="(item, index) of page.items" class="active_page"    :key="index">{{item.name}}</b-navbar-item> -->
                <StatusBar></StatusBar>
            </div>
            <b-navbar-item v-if="user == '86'" tag="router-link" :to="{name: 'help'}">
                Помощь
            </b-navbar-item>
        </template>

        <template #end>
            <b-navbar-item tag="div">
                <div class="buttons">  
                    <div v-if="user == 86" class="navbar-item has-divider is-desktop-icon-only" title="Dark mode" @click="darkModeToggle">
                        <b-icon :icon="darkModeToggleIcon" custom-size="default"/>
                    </div> 
                    <users ></users>
                    <profile></profile>
                    <chat :unread="unread" ></chat>
                    <a class="button is-warning" @click="open_tasks">
                        Задачи
                    </a>
                    <a class="button is-light" @click="logout">
                        Выход
                    </a>
                </div>
            </b-navbar-item>
        </template>
    </b-navbar>
</template>

<script>
import axios from 'axios'
import { mapState } from 'vuex'
  
import profile from '@/components/profile.vue'
import online_users from '@/components/online_users.vue'
import messenger from '@/components/messenger.vue'
import StatusBar from "@/components/status_bar.vue"

export default ({

    components:{
        'chat': messenger,
        'profile': profile,
        'users': online_users,
        StatusBar
    },
    props:['unread'],
    data(){
        return{
            role: this.$store.getters.USER_DEPART,
            // pages: this.$store.getters.USER_PAGES,
            // pages: [1, 2]
            default_page: '',
            user: this.$store.getters.USER_ID,
            active: '',
            url: this.$store.getters.api_url_new,
            stats:{
                "Проектирование":'',
                "Производство":''
            }
        }
    },

    computed: {
        user_role: function(){
            return this.$store.getters.USER_ROLE
        },

        user_depart: function(){
            return this.$store.getters.USER_DEPART
        },

        pages: function(){
            return this.$store.getters.USER_PAGES
        },

        pages_count: function(){
            let count = 0
            Object.values(this.$store.getters.USER_PAGES).forEach(el => {
                el.items.forEach(element => {
                    if(!element.hidden) count++
                });
                
            });
            return count
        },

        darkModeToggleIcon () {
            return this.is_dark_mode_active ? 'white-balance-sunny' : 'weather-night'
        },

        ...mapState([
            'is_dark_mode_active'
        ])
        
    },

    methods:{

        reload_page: function(){
            this.$router.go(0)
        },

        set_page: function(item){
            
            if(item.name === this.active){
                this.$router.go(0)
            }else{
                this.active = item.name
                this.$router.push({name: item.path})
            }
            // app.$router.go(0)
        },

        open_tasks(){
            this.$router.push({name:'tasks', params:{ user: this.$store.getters.USER_ID}})
        },

        
        logout(){
            axios
                .get( this.url, {params: {cmd: 'logs', user: this.user, t: new Date() }})
                .then(() =>{
                })
            this.$store.dispatch('logout')
            localStorage.clear()

            this.$router.push('/check_access')
        },

        darkModeToggle () {
            this.$store.dispatch('dark_mode_toggle')
        },
    }

})
</script>

<style scoped>
    .departs_ {
        display: flex;
        align-items: flex-end;
    }

    .topped {
        border-bottom: 1px solid gray;
    }

    .active_page {
        /* background: red; */
        border: 1px solid gray;
        height: 50px;
        border-bottom: none;
        border-radius: 0px 20px 0px 0px;
        border-right: none;
        background: #ececec;
        min-width: 90px;
        text-align: center;
        justify-content: center;
        color: unset;
        transition: all 0.25s ease-in-out 0s;
    }

    /* .active_page:hover {
        
        opacity: 1;
        cursor: pointer;
        background-color: rgb(238, 238, 238);
        color: unset
    } */

    .active_list {
        background: rgb(210, 210, 210);
        box-shadow: rgb(0 0 0 / 12%) 0px 1px 3px, rgb(0 0 0 / 24%) 0px 1px 2px;
    }

    .active_page:last-child{
        border-right: 1px solid gray;
    }

    .indicator{
        position: absolute;
        top: -36px;
        left: 164px;
    }

    .status_position{
        width: 302px;
        height: 36px;
        margin-left: 50px;
        white-space: pre;
    }

    .greeny{
        color: rgba(114, 146, 0, 0.938)
    }

    .yellow{
        color: yellow
    }

    .hidden_pages{
        border: 1px solid gray;
        height: 50px;
        border-bottom: none;
        border-radius: 0px 20px 0px 0px;
        background: #ececec;
        text-align: center;
        justify-content: center;
        color: unset;
        transition: all 0.25s ease-in-out 0s;
    }

    >>>.navbar-link{
        border-radius: 0px 20px 0px 0px;
    }

    /*...............Медиа запросы.................*/


        @media (min-width: 0px) and (max-width: 1024px) {
        .departs_{
           flex-direction: column;
           align-items :flex-start !important;
       }
       .navbar-menu.is-active {
            display: block;
            position: absolute;
            width: 100%;
    }
        .navbar-menu.is-active {
            display: block;
            position: absolute;
            width: 100%;
    }
  
        .buttons>div:nth-child(-n+2) {
            display: none;
    }
     /*   .is-warning {
            display: none;
    }*/
        .navbar-item {
            width: 100%;
    } 
        .departs_>.navbar-item>.navbar-dropdown>.navbar-item {
            width:100%;
            background: #d0cfce47;
    }
        .navbar-link{
            border-radius: 0px !important;
        }
        .navbar-dropdown {
             padding-top: 0rem; 
    }
}
       
</style>