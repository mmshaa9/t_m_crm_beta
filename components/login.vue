<template>
    <div class="auth_area">
        <div class="container_list" >
            <div class="container_card mat_des_shadow">
                <div class="image_logo">
                    <img src="/crm/frontend/img/logo_big.png">
                </div>
                <form class="form_new_login" @submit.prevent="login">
                    <div class="new_login">
                        <label for="login">Сотрудник</label>
                        <b-select id="login" v-model="username" placeholder="Выберите сотрудника">
                            <option v-for="(worker, index) in workers" :value="worker.хеш" :key="index">
                                {{worker.Отдел + ' - ' + worker.Сотрудник}}
                            </option>                            
                        </b-select>
                        <label for="pass">Пароль</label>
                        <b-input v-model="password" type="password" value="" password-reveal autocomplete="on">
                        </b-input>  
                        <b-button type="is-warning" @click="login" class="button_width">Вход</b-button>
                        <b-button @click="mail_new_pass"  class="button_width restore_pass">Отправить пароль на почту</b-button> 
                    </div>
                </form>
            </div>
            <!-- <b-field label=" " horizontal> -->
            <!-- </b-field> -->
            <b-loading :is-full-page="isFullPage" v-model="isLoading" :can-cancel="false"></b-loading>
    </div>
</template>

<script>
  module.exports = {
    name: 'login',
    data(){
      return {
        workers: [] ,
        username : "",
        password : "",
        url: app.$store.getters.api_url_new,
        isLoading: false,
        isFullPage: true
      }
    },

    mounted: function(){
        this.loadData()
        this.workers
        localStorage.clear()
    },
    methods: {
        get_list: function(data){
            this.workers = data
        },

        loadData: function(){
            var get_list = this.get_list
            axios
            .get( this.url, {params: {cmd: 'get_worker_list', t: new Date() }})
            .then( function(response){
                // console.log(workers)
                get_list(response.data)
                // console.log(response.data)
            })

        },


        login: function(){
            if(this.username == '' || this.password == '') return app.alert('Заполните все поля', 'is-warning')
            this.isLoading = true
            var bodyFormData = new FormData()
            var user = this.username
            var danger = this.danger
            var wrong_pass = this.wrong_pass
            var password = this.password
            bodyFormData.append('cmd', 'authorization')
            bodyFormData.append('user', user)
            bodyFormData.append('password', password)
            var authUser = {}

            axios({
                method: 'post',
                url: this.url,
                data: bodyFormData,
                headers: {'Content-Type': 'multipart/form-data' }
                })
            .then( (response) => {
                console.log(response.data)
                if(response.data == 'Поля не должны быть пустыми!'){
                    this.isLoading = false
                    danger()
                }else if(response.data.result == '-error'){
                    this.isLoading = false
                    app.alert('Неверный пароль', 'is-warning') 
                }else{
                //     const token = response.data.token
                    const user_id = response.data.id
                    const user_role = response.data.role
                    const user_depart = response.data.depart
                    const user_group = response.data.group
                    const user_pages = response.data.pages
                    const default_page = response.data.default_page
                    this.isLoading = false
                    app.$store.dispatch('SET_ID', user_id)                    
                    app.$store.dispatch('SET_ROLE', user_role)
                    app.$store.dispatch('SET_AUTH', true)
                    app.$store.dispatch('SET_DEPART', user_depart)
                    app.$store.dispatch('SET_GROUP', user_group)
                    app.$store.dispatch('SET_PAGES', user_pages)
                    router.push({name: default_page})                     
                }
            })
            .catch((e)=>{
                console.log(e)
            })
        }, 

        danger: function() {
            this.$buefy.toast.open({
                duration: 2000,
                message: 'Поля не должны быть пустыми',
                position: 'is-top-right',
                type: 'is-danger'
            })
        },

        mail_new_pass: function(){
            // return
            if(this.username == '') return app.alert('Выберите пользователя', 'is-warning')
            this.isLoading = true

            var bodyFormData = new FormData()
            var user = this.username
            bodyFormData.append('cmd', 'mail_new_pass')
            bodyFormData.append('user', user)
            axios({
                method: 'post',
                url: this.url,
                data: bodyFormData,
                headers: {'Content-Type': 'multipart/form-data' }
                })
            .then( (response) => {
                console.log(response.data)
                if(response.data.result == '-noemail'){
                    this.isLoading = false
                    app.modal_alert('', 'У Вас не указана почта', 'Печаль(')
                }else{
                    this.isLoading = false
                    app.modal_alert('', 'Ваш пароль отправлен на почту, указанную в программе. Если Вы не получили письмо, свяжитесь с системным администратором.', 'Ок')
                }
            })
            .catch((e)=>{
                console.log(e)
            })
        },

        wrong_pass: function() {
            this.$buefy.toast.open({
                duration: 2000,
                message: 'Неверный пароль',
                position: 'is-top-right',
                type: 'is-warning',
            })
        },
    }  
  }
</script>

<style scoped>

.auth_area{
    height: 100%;
}

.container_list{
    justify-content: center;
    padding: unset;
}

.button_width{
    white-space: pre;
    overflow: unset;
}

.form_new_login{
        width: 100%;
    }

.container_card{
    padding: 15px;
    border-radius: 10px;
    background: white;
    width: 20%;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.new_login{
    display: flex;
    flex-direction: column;
}

.new_login>*{
    margin-bottom: 5px;
}

.new_login>button{
    margin: 0;
    border: 1px solid lightgray;
    height: unset;
    margin-top: 5px;
}

.select{
    width: 100%;
}

select{
    width: 100%;
}

/*...........медиа запрсоы............*/



@media (min-width: 0px) and (max-width: 500px) {
     .container_card {
        width: 90% !important;
    }
}

 @media (min-width: 501px) and (max-width: 720px) {
       .container_card {
        width: 80% !important;
    }
 }

   @media (min-width: 721px) and (max-width: 920px) {
     .container_card {
        width: 60% !important;
    }
   }
    @media (min-width: 921px) and (max-width: 1200px) {
      .container_card {
        width: 44% !important;
    } 
 }

</style>