<template>
    <div class="basis_card">
        <div class="content_card">
            <div class="content_card_block_one">
                <div class="content_card_block_one_roster">
                    <div class="content_card_block_one_list">
                        <div class="list_text">Код</div>
                        <div class="content_card_block_one_list_inp">
                            <input readonly class="list_inp tm_input" type="text" v-model="worker.id">
                        </div>
                    </div>
                    <div class="content_card_block_one_list">
                        <div class="list_text">ФИО</div>
                        <div class="content_card_block_one_list_inp">
                            <input class="list_inp tm_input" v-model="worker.name" type="text">
                        </div>
                    </div>
                    <div class="content_card_block_one_list">
                        <div class="list_text">Отдел</div>
                        <div class="content_card_block_one_list_inp">
                            <select class="list_inp tm_input"  v-model="worker.depart">
                                <option v-for="depart in departs" :key="depart.Код" :value="depart.Код">{{depart.Отдел}}</option>
                            </select>
                        </div>
                    </div>
                    <div class="content_card_block_one_list">
                        <div class="list_text">Группа</div>
                        <div class="content_card_block_one_list_inp">
                            <select class="list_inp tm_input" v-model="worker.org_group">
                                <option v-for="group in groups" :key="group.Код" :value="group.Код">{{group.группа}}</option>
                            </select>
                        </div>
                    </div>
                    <div class="content_card_block_one_list">
                        <div class="list_text">Руководитель отдела </div>
                        <div class="content_card_block_one_list_inp">
                            <select class="list_inp tm_input" v-model="worker.head_depart">
                                <option value="1">Да</option>
                                <option value="0">Нет</option>
                            </select>
                        </div>
                    </div>
                    <div class="content_card_block_one_list">
                        <div class="list_text">Статус</div>
                        <div class="content_card_block_one_list_inp">
                            <input class="list_inp tm_input" type="text">
                        </div>
                    </div>
                </div>
                <div class="content_card_block_img">
                    <div class="content_card_block_img_photo">
                        <img class="new_photo" :src="worker.photo">
                    </div>
                    <div class="content_card_block_img_butt">
                        <input id="img-btn" hidden type="file" @change="open_nev_photo" accept="image/*" >
                        <label class="img_butt" for="img-btn">Добавить фото</label>
                    </div>
                </div>
            </div>
            <div class="content_card_block_two">
                <div class="block_heading">Контактная информация</div>
                <div class="content_card_block_two_list">
                    <div class="list_text2">Телефон</div>
                    <input class="list_inp2 tm_input" v-phone type="text"  v-model="worker.phone">
                </div>
                <div class="content_card_block_two_list">
                    <div class="list_text2">Почта</div>
                    <input class="list_inp2 tm_input" type="text"  v-model="worker.mail">
                </div>
            </div>
            <div class="content_card_block_three">
                <div class="block_heading">Оклад и режим</div>
                <div class="content_card_block_three_list">
                    <div class="list_text3">Оклад</div>
                    <input class="list_inp3 tm_input" type="text" v-digit v-model="worker.salary">
                </div>
                <div class="content_card_block_three_list">
                    <div class="list_text3">Режим</div>
                    <div class="content_card_block_three_inp">
                        <input class="list_inp3 tm_input" type="text" v-model="worker.mode">
                        <button class="content_card_block_three_butt button" @click="change_mode">...</button>
                    </div>
                </div>
            </div>
            <div class="content_card_block_four">
                <div class="block_heading">Авторизация</div>
                <div class="content_card_block_four_content">
                    <div class="content_card_block_four_block">
                        <div class="content_card_block_four_list1">
                            <div class="list_text4">Через программу</div>
                            <select class="list_inp4 tm_input" v-model="worker.entry_crm" @change="opt_entry">
                                <option value="1">Да</option>
                                <option value="0">Нет</option>
                            </select>
                        </div>
                        <div class="content_card_block_four_list2">
                            <div class="list_text4">Через терминал</div>
                            <select class="list_inp4 tm_input"  v-model="worker.entry_terminal">
                                <option value="1">Да</option>
                                <option value="0">Нет</option>
                            </select>
                        </div>
                    </div>
                    <div class="content_card_block_four_block hidden_element">
                        <div class="content_card_block_four_code">
                            <div class="list_text4">Пароль </div>
                            <input class="list_inp4 tm_input imp4" type="password" v-model="worker.password_web">
                        </div>
                        <div class="content_card_block_four_code">
                            <div class="list_text4">Штрихкод</div>
                            <input class="list_inp4 tm_input imp4" type="text" v-model="worker.barcode_text">
                        </div>
                    </div>
                </div>
            </div>
            <div class="content_card_block_five">
                <div class="content_card_block_five_head">
                    <div class="content_card_block_five_head_text">Файл</div>
                    <button  class="content_card_block_five_head_butt button" @click="attach_file(worker)">Прикрепить </button>
                </div>
                <div class="content_card_block_five_list">
                    <attachments :user="user" :data="{ empty: true, items: files || []}" @open-attachment ="open_attachment($event)" @del-attachment = "del_attachment($event)"></attachments>
                </div>
            </div>
            <div class="content_card_buttons">
                <button class="content_card_butt button" @click="confirm_delete_worker" :disabled="worker.is_working == 0">Уволить</button>
                <div class="content_card_buttons_butt">
                    <button class="content_card_butt button" @click="confirm_change_worker">ОК</button>
                    <button class="content_card_butt button" @click="$parent.obj.isactive = false">Отмена</button>
                </div>
            </div>
        </div>
        <b-loading :is-full-page="false" v-model="loading" :can-cancel="false"></b-loading>
    </div>
</template>

<script>
    import { mapActions } from 'vuex'
    import my_attachments from '@/components/my_attachments.vue?t='
    import * as my_f from "@/my"
    export default ({
        props:['data'],

        directives: {
            digit :{
                inserted: function(el) { 
                    el.oninput = function(e) {
                        if (!e.isTrusted) {
                            return;
                        }
                        
                        let x = this.value.replace(/\D/g, '').match(/(\d)(\d{0,1})(\d{0,1})(\d{0,1})(\d{0,1})(\d{0,1})(\d{0,1})(\d{0,1})(\d{0,1})(\d{0,1})(\d{0,1})/)
                        // return
                        if(x){
                            this.value =  x[0].length <= 3 ? x[0] : (x[0].length ==4 ? x[1] + " " + x[2] + x[3] + x[4] : x[0].length ==5 ? x[1] + x[2] + " " + x[3]+x[4]+x[5] : x[0].length == 6 ? x[1] +x[2] + x[3] + " " + x[4] + x[5] +x[6] : x[0].length == 7 ? x[1] + " " + x[2] + x[3] + x[4] + " " + x[5] + x[6] + x[7] : x[0].length == 8 ? x[1] + x[2] + ' ' + x[3] + x[4] + x[5] + ' ' + x[6] + x[7] + x[8] : x[0].length == 9 ? x[1] + x[2] + x[3] + ' ' + x[4] + x[5] + x[6] + ' ' + x[7] + x[8] + x[9] : x[0]) 
                        }else{
                            this.value = ''
                        }
                        
                        el.dispatchEvent(new Event('input'));
                    }
                }
            },
            phone: {
                bind: function (el) {
                    el.oninput = function (e) {
                        let y = e;
                        if (!e.isTrusted) {
                            return;
                        }
                        let x = y.target.value
                            .replace(/\D/g, "")
                            .match(
                                /(\d)(\d{0,3})(\d{0,3})(\d{0,2})(\d{0,2})(\d{0,2})/
                            );
                        if (x) {
                            y.target.value =
                                (x[1] ? x[1] : "") +
                                (x[2] ? "-" + x[2] : "") +
                                (x[3] ? "-" + x[3] : "") +
                                (x[4] ? "-" + x[4] : "") +
                                (x[5] ? "-" + x[5] : "") +
                                (x[6] ? "-" + x[6] : "");
                        } else {
                            y.target.value = "";
                        }
                        el.dispatchEvent(new Event("input"));
                    };
                },
            },
        },
        components:{
        'attachments': my_attachments,
        },
        data(){
            return {
                url: null,
                worker: {
                    barcode_text: null,
                    depart: null,
                    entry_crm: null,
                    entry_terminal: null,
                    head_depart: null,
                    id: null,
                    is_working: null,
                    mail: null,
                    mode: null,
                    mode_id: null,
                    msa_record_id: null,
                    msa_record_page_id: null,
                    name: null,
                    name2: null,
                    org_group: null,
                    password_web: null,
                    phone: null,
                    photo: null,
                    salary: null,
                },
                files:[],
                groups: '',
                modes: '',
                departs: '',
                loading: false,
                user: this.$store.getters.USER_ID
            }  
        }, 

        computed:{
            id(){
                return this.data
            }
        },

        mounted(){
            this.load_info()
            this.load_data()
        },
        
        methods: {
            ...mapActions([
                'show_alert',
                'confirm',
                'select'
            ]),

            load_info(){
                if(this.id != null){
                    this.loading = true
                    this.$_send_({cmd:'staff_employee_get_info', user_id: this.id.id}, (response)=>{
                        this.loading = false
                        this.worker = response.data.worker
                        this.files = response.data.files
                    })
                }
            },

            load_data(){
                this.$_send_({cmd:'staff_get_org_employees_digest'}, (response)=>{
                    this.groups = response.data.groups
                    this.modes = response.data.modes
                    this.departs = response.data.departs
                })
            },

            open_nev_photo(e){
                var reader = new FileReader()
                reader.readAsDataURL(e.target.files[0])
                reader.onload = () => {
                    // console.log(reader.result)
                    this.worker.photo = reader.result
                };
                // const file = e.target.files[0];
                // this.worker.photo = btoa(URL.createObjectURL(file));
            },
            
            save(){
                this.loading = true
                this.$_send_({cmd:'staff_employee_save_info', user_id:this.worker.id ?? null, worker: JSON.stringify(this.worker)}, (response)=>{
                    this.loading = false
                    if(response.data.result == "-ok"){
                        this.show_alert({text:'Информация о сотруднике сохранена', isactive: true, action: ()=>{this.$parent.obj.isactive = false}})
                        if(this.worker.id != null){
                            this.$emit('update_worker', response.data.info)
                        }
                    }else{
                        this.show_alert({text:'Не удалось выполнить операцию, попробуйте позже', isactive: true, action: ()=>{return}})
                    }
                })
            },

            change_mode(){
                this.select({
                    items:this.modes,

                    set_action: (option)=>{
                        // this.turns_action(option, day)
                        this.set_mode(option)
                    },
                })
            },

            set_mode(option){
                this.worker.mode = option.название
                this.worker.mode_id = option.Код
            },

            set_fired(){
                this.$_send_({cmd: "employee_fired", user: this.worker.id}, (response)=>{
                    if(response.data.result == "-ok"){
                        this.$alert('Операция успешно завершена', 'is-success')
                        this.$parent.obj.isactive = false
                    }else{
                        this.$alert('Не удалось уволить менеджера, попробуйте позже', 'is-warning')
                    }
                })
            },

            
            value_num(){
                
            },
            confirm_change_worker(){
                let tmp = this.check_save()
                if(tmp.length>0) return this.$alert(tmp, 'is-warning')
                this.confirm({
                    text: this.id != null ? "Вы действительно хотите изменить сотрудника?" : "Вы действительно хотите сохранить сотрудника?",
                    confirm_action:()=>{this.save()}
                })
            },
            // Проверка полей воода
            opt_entry(){
                if(this.worker.entry_crm == 0) return  this.worker.password_web = ''
            },
            check_save(){
                if(this.worker.name == null || this.worker.name.length == 0) return 'Заполните поле ФИО'
                if(this.worker.depart == null || this.worker.depart.length == 0) return 'Заполните отдел'
                if(this.worker.org_group == null || this.worker.org_group.length == 0) return 'Заполните группу'
                if(this.worker.salary == null || this.worker.salary.length == 0) return 'Заполните оклад'
                if(this.worker.mode == null || this.worker.mode.length == 0) return 'Заполните режим'
                if(this.worker.entry_crm == null) return 'Выберете тип авторизации'
                if(this.worker.entry_terminal == null) return 'Выберете тип авторизации'
                // if(this.worker.entry_crm == 1 && (this.worker.password_web == null || this.worker.password_web.length == 0)) return 'Заполните пароль'
                // if(this.worker.entry_crm == 1 && this.worker.password_web.length < 3) return 'Пароль должен быть больше трех символов'
                return ''
            },

            confirm_delete_worker(){
                if(this.id != null){
                    this.confirm({
                        text: "Вы действительно хотите уволить сотрудника?",
                        confirm_action:()=>{this.set_fired()}
                    })
                }else{
                    this.$alert('Уволить можно только существующего сотрудника' , 'is-warning')
                }
            },


            // Прикрепление файла
            attach_file(){
                if(this.worker.id == null) return this.$alert('Сначала сохраните сотрудника' , 'is-warning')
                this.select({
                    items:[
                        {name: 'Трудовая книжка'},
                        {name: 'Паспорт'}
                    ],
                    set_action: (option)=>{
                        // this.turns_action(option, day)
                        this.add_file(option)
                    },
                })
            },

            add_file(option){
                my_f.selectFile(files =>{
                    for (var i = 0; i < files.length; i++) {
                        my_f.uploadFile({cmd: 'staff_employee_attach_file', userfile: files[i], user:this.user, user_id: this.worker.id, doc_type: option.name}, (response) =>{
                            if(response.data.result == "-ok"){
                                this.$alert("Документ загружен", 'is-success')
                                this.files = response.data.files
                            }else{
                                this.$alert("Не удалось загрузить документ, попробуйте позже", 'is-warning')   
                            }
                        },this.$store.getters.api_url_new)
                    }
                })
                
            },

            open_attachment: function(file){
                my_f.file_operation('open', file.link)
            },   

            del_attachment: function(id){
                console.log('del')
                console.log(id) 
                this.$_send_({cmd: "staff_employee_delete_file", file_id: id, user_id: this.worker.id}, (response)=>{
                    if(response.data.result == "-ok"){
                        this.$alert('Файл удален', 'is-success')
                        this.files = response.data.files
                    }else{
                        this.$alert('Не удалось удалить файл, попробуйте позже', 'is-warning')
                    }
                })
            },
        },
    })
</script>

<style scoped>
    button{
        cursor: pointer;
    }
  
    /* тело карточки */
    .basis_card{
        width: 700px;
    }

    .content_card {
        padding: 6px;
    }

    /* болок 1 */

    .content_card_block_one {
        display: flex;
        margin: 5px 0px 0px 5px;    
    }
    .content_card_block_one_list {
        margin-bottom: 10px;
        display: flex;
        width: 100%;
        justify-content:space-between;
    }
    .content_card_block_one_roster{
        width: 70%;
    }
    .content_card_block_img {
        width: 30%;
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    .list_text{
        width: 65%;
    }
    .content_card_block_one_list_inp{
        width: 100%;
    }
    .list_inp{
        width: 100%;
        padding: 0px 5px;
        height: 25px;
    }
    .content_card_block_img_photo {
        height: 170px;
        width: 180px;
        border-style: solid;
        border-color: lightgray;
        display: flex;
        justify-content: center;
        margin-bottom: 9px;
    }
    .img_butt{
        border: 1px solid lightgray;
        border-radius: 4px;
        font-size: 14px;
    }
    .new_photo{
        /* max-height: 130px;
        max-width: 150px; */
        object-fit: cover;
    }

    /* блок 2 */

    .block_heading {
        font-weight:bolder; 
        margin: 5px 0px 10px 5px;
    }
    .content_card_block_two_list {
        display: flex;
        justify-content:space-between;
        margin: 5px 10px 0px 5px;
    }
    .list_text2{
        width: 40%;
    }
    .list_inp2{
        width: 100%;
        padding: 0px 5px;
        height: 25px;
    }
     
    /* блок 3 */

    .content_card_block_three_list {
        display: flex;
        justify-content:space-between;
        margin: 5px 10px 0px 5px;
    }
    .content_card_block_three_inp{
        display: flex;
        width: 100%;
    }
    .list_text3{
        width: 40%;
    }
    .list_inp3{
        width: 100%;
        padding: 0px 5px;
        height: 25px;
    }
    .content_card_block_three_butt{
        min-width: 20px;
        height: 25px;
        margin: 0px;
    }
    
    /* блок 4 */


    .content_card_block_four_content{
        display: flex;
        justify-content:space-between;
        margin: 0px 10px 0px 5px;
    }
    .content_card_block_four_block{
        width: 100%;
        display: flex;
        justify-content: space-between;
    }

    .content_card_block_four_list1 {
        width: 47%;
        display: flex;
    }
    .content_card_block_four_list2 {
        width: 40%;
        display: flex;
    }
    .content_card_block_four_code {
        margin: 0px 0px 5px 30px;
        display: flex;
    }
    .list_text4{
        width: 60%;
    }
    .list_inp4{
        width: 40%;
        max-width: 120px;
        padding: 0px 5px;
        height: 25px;
    }
    .imp4{
        width: 90%;
    }

    


    /* блок 5 */

    .content_card_block_five {
        margin: 10px 10px 0px 5px;
        min-height: 100px;
        border: 1px solid #8a8a8a;
    }

    .content_card_block_five_head {
        border-bottom: 1px solid #8a8a8a;
        display: flex;
        justify-content: space-between;
    }
    .content_card_block_five_head_butt{
       height: 25px;
       margin: 0px;
       border-radius: 0px;
       border: none;
       border-left: 1px solid #8a8a8a;
      
    }

    .content_card_block_five_list {
        display: flex;
        border-top: none;
        height: 75px;
    }

    .content_card_block_five_text {
        margin-left:5px;
        text-align: center;
    }
    .content_card_block_five_tick{
        border: none;
        border-right: 1px solid #8a8a8a;
    }
    .content_card_block_five_head_text {
        padding-left: 5px;
    }

    /* кнопки */
    .content_card_buttons_butt{
        display: flex;
        justify-content: space-between;
        width: 175px;
    }
    .content_card_buttons {
        padding: 10px;
        display: flex;
        justify-content:space-between;
    }
    .content_card_butt {
        height: 30px;
        margin: 0px;
        min-width: 20px;
    }
    label {
        background-color: hsl(0deg, 0%, 100%);
        border-color: hsl(0deg, 0%, 86%);
        border-width: 1px;
        color: hsl(0deg, 0%, 21%);
        cursor: pointer;
        justify-content: center;
        padding-left: 1em;
        padding-right: 1em;
        text-align: center;
        white-space: nowrap;
        height: 25px;
        border-left: 1px solid #8a8a8a ;
    }

    >>>button.btn {
        display: none;
    }
    >>>.attachments-block {
        padding: 0px;
        overflow: auto;
        width: 100%;
    }
    >>>.attachments-items>div {
        border: 1px solid #62626215;
    }

</style>