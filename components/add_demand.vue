<template>
    <div class="modal_block">
        <span class="modal-paragraph-header">Общая информация</span>

        <input
            type="text"
            placeholder="Почта получателя"
            v-model="new_demand.mail_adress_1"
            tabindex="1"
            class="tm_input"
        />
        <div class="mail_recipient">
            <button class="promt_text" v-for="(mail, index) in mails" @click="new_demand.mail_adress_1 = mail" :key="index">
                {{ mail }}
            </button>
        </div>

        <input
            type="text"
            placeholder="Почта отправителя"
            v-model="new_demand.mail_adress_2"
            @change="email_normalization"
            tabindex="2"
            class="tm_input"
        />
        <textarea
            rows="2"
            maxlength="2000"
            placeholder="Текст сообщения"
            v-model="new_demand.mail_message"
            tabindex="3"
            class="tm_input"
            :class="{textarea_big: new_demand.cmd == 'demand_edit_item'}"
        ></textarea>
        <div class="keywords_wrapper">
            Количество символов (мак. 2000): {{ message_length }}
        </div>

        <div class="keywords_wrapper">
            <input
                type="text"
                placeholder="Ключевые слова (через запятую)"
                v-model="new_demand.keywords"
                ref="filter"
                tabindex="4"
                class="tm_input"
            />
            <div class="keywords_promt_wrapper">
                <div
                    class="promt_text"
                    v-for="val in post_list"
                    :key="val"
                    @click="ins_keyword(val)"
                >
                    {{ val }}
                </div>
            </div>
        </div>
        <span class="modal-paragraph-header">Организация</span>
        <input
            type="text"
            placeholder="Название"
            v-model="new_demand.org_name"
            @change="org_name_normalization"
            tabindex="5"
            class="tm_input"
        />
        <input
            type="text"
            placeholder="Город"
            v-model="new_demand.org_adress"
            @change="find_original_clients"
            tabindex="6"
            class="tm_input"
        />
        <span class="modal-paragraph-header">Контактая информация</span>
        <input
            type="text"
            placeholder="Телефон"
            v-model="new_demand.contact_tel"
            maxlength="25"
            @change="find_original_clients"
            v-phone
            tabindex="7"
            class="tm_input"
        />
        <input
            type="text"
            placeholder="Имя"
            v-model="new_demand.contact_name"
            tabindex="8"
            class="tm_input"
        />
        <input
            type="text"
            placeholder="Должность"
            v-model="new_demand.contact_position"
            tabindex="9"
            class="tm_input"
        />
        <div class="rang_task" v-if="!new_demand.self">
            <input
                type="checkbox"
                v-model="new_demand.archival"
                tabindex="10"
                class="tm_input"
            />
            <span>Архивная заявка</span>
        </div>

        <div class="rang_task" v-if="!new_demand.self">
            <input
                type="checkbox"
                v-model="new_demand.important"
                tabindex="11"
                class="tm_input"
            />
            <span>Важная заявка</span>
        </div>

        <input
            type="text"
            placeholder="Предполагаемая стоимость заявки"
            v-model="new_demand.price"
            tabindex="12"
            class="tm_input"
        />
        <div class="same_clients" v-if="new_demand.cmd == 'demand_add_item' || new_demand.cmd == 'create_demand_to_manager'">
            <div class="rang_task add_new_client_opt">
                <b-switch v-model="new_demand.add_new_client"
                    size="is-small"
                    @change.native="set_client_empty">
                        Создать нового клиента на основе введенных данных
                </b-switch>
            </div>
            <div class="same_clients_list" :class="{disabled: new_demand.add_new_client, search_done: !isLoading}">
                <div v-for="client in new_demand.origin_client" :key="client.Код" @click="set_client(client)" class="same_client_row" :class="{active_client: new_demand.client == client.Код}">
                    <div >{{client.Название + ", " + client.Адрес + ", "}} <span class="bold">{{client.Контакт}}</span>{{", " + "заказы:" + (client.заказы_признак == 1 ?  " есть" : " нет")}}</div>
                </div>
                <b-loading v-model="isLoading" :is-full-page="false">
                    <span v-if="!finded_same">Совпадения не найдены
                    </span>
                </b-loading>
            </div>
        </div>
        <b-button size="is-small" type="is-warning" class="button-confirm" @click="confirm_save">Ок</b-button>
        <b-button size="is-small" class="button-cancel" @click="$parent.obj.isactive = false">
            Отмена
        </b-button>
    </div>
</template>

<script>
import axios from 'axios'
import { mapActions } from 'vuex'

export default {
    
    props:{
        data:{
            type:Object,
            require: true
        }
    },

    directives: {
        phone: {
            bind: function (el) {
                el.oninput = function (e) {
                    if (!e.isTrusted) {
                        return;
                    }
                    console.log(this);
                    let x = this.value
                        .replace(/\D/g, "")
                        .match(
                            /(\d)(\d{0,3})(\d{0,3})(\d{0,2})(\d{0,2})(\d{0,2})/
                        );

                    if (x) {
                        this.value =
                            (x[1] ? x[1] : "") +
                            (x[2] ? "-" + x[2] : "") +
                            (x[3] ? "-" + x[3] : "") +
                            (x[4] ? "-" + x[4] : "") +
                            (x[5] ? "-" + x[5] : "") +
                            (x[6] ? "-" + x[6] : "");
                    } else {
                        this.value = "";
                    }
                    el.dispatchEvent(new Event("input"));
                };
            },
        },
    },

    data(){
        return{
            mails: [
                "tulmash@mail.ru",
                "tulmesh@mail.ru",
                "info@tulmash.ru",
                "info@tulpech.ru",
                "info@tulmesh.ru",
                "info@tulpress.ru",
                "info@tulteh.ru",
            ],
            org_forms: [
                "ФГУП",
                "ГБУ",
                "ГКУ",
                "ГУП",
                "ГУЧ",
                "Д(С)У",
                "ИП",
                "МУП",
                "МКП",
                "МУУП",
                "МКУ",
                "МБУ",
                "МУЧ",
                "ОАО",
                "ЗАО",
                "ООО",
                "ОДО",
                "ПАО",
                "ТОО",
                "АО",
                "ИП",
                "ГУ",
            ],
            new_demand: {
                mail_adress_1: this.data.mail_adress_1,
                mail_adress_2: this.data.mail_adress_2,
                mail_message: this.data.mail_message,
                org_name: this.data.org_name,
                org_adress: this.data.org_adress,
                contact_tel: this.data.contact_tel,
                contact_name: this.data.contact_name,
                contact_position: this.data.contact_position,
                user_id: this.data.user_id,
                keywords: this.data.keywords,
                archival: this.data.archival,
                id: this.data.id,
                visible: false,
                client: null,
                add_new_client: false,
                cmd: this.data.cmd,
                important: this.data.important,
                price: this.data.price,
                self: this.data.self ?? false,
                origin_client:[]
            },

            pre_list: [
                "Щебень",
                "Камень",
                "Щебенка",
                "Гипс",
                "Мел",
                "Базальт",
                "Торф",
                "Кирпич",
                "Кость",
                "Бетон",
                "Клинкер",
                "Стекло",
            ],
            isLoading: false,
            finded_same: true,
            url: this.$store.getters.api_url_new
        }
    },

    mounted(){
        
    },

    computed:{
        
        message_length: function () {
            return this.new_demand.mail_message.length;
        },

        post_list: function () {
            // возвращает список подсказок
            var search = this.new_demand.keywords;

            // получаем последнее слово в строке
            search = search.split(",");
            search = search[search.length - 1];
            search = search.trim();

            if (search == "") return [];
            //console.log(search)
            // формируем массив с совпадениями
            var res = this.pre_list.filter(function (el) {
                return el.trim().toLowerCase().startsWith(search.toLowerCase());
            });

            // сортируем
            res.sort();
            // возвращаем первые 5 совпадений
            res = res.slice(0, 5);

            return res;
        },
    },

    methods:{

        ...mapActions([
            'show_alert',
            'confirm'
        ]),

        email_normalization: function () {
            // нормализаци почты отправителя
            var tmp = this.new_demand.mail_adress_2;
            tmp = tmp.trim();
            //tmp = tmp.toLowerCase()
            this.new_demand.mail_adress_2 = tmp;
            this.find_original_clients()
        },

        confirm_save(par){
            if(this.new_demand.cmd != 'demand_edit_item'){
                if(this.new_demand.client == null && !this.new_demand.add_new_client){
                    return this.show_alert({text:'Выберите клиента из списка или создайте нового при отсутствии совпадений', isactive: true, action: ()=>{return}})
                }
                if(this.new_demand.price == '' || this.new_demand.price < 0 || this.new_demand.price == undefined){
                    return this.show_alert({text:'Укажите стоимость заявки', isactive: true, action: ()=>{return}})
                }
            }
            let text
            if(this.new_demand.add_new_client){
                text = "Вы действительно хотите создать нового клиента и завести на него заявку?"
            }else if(this.new_demand.cmd != 'demand_edit_item'){
                text = "Вы действительно хотите завести заявку на выбранного клиента?"
            }else{
                text = "Вы дейтвительно хотите изменить заявку?"
            }
            this.confirm({
                text: text,
                confirm_action:()=>{this.save_demand()}
            })
        },

        save_demand: function () {
            if (
                !/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(
                    this.new_demand.mail_adress_1
                )
            ) {
                // this.$alert('Укажите корректный адрес, на который пришла заявка и попробуйте снова')
                // return false
            }

            if (
                !/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(
                    this.new_demand.mail_adress_2
                )
            ) {
                // this.$alert('Укажите корректный адрес, с которого пришла заявка и попробуйте снова')
                // return false
            }

            if (this.new_demand.mail_message == "") {
                this.$alert("Укажите текст заявки и попробуйте снова", 'is-warning');
                return false;
            }

            // сохраняем новую заявку
            var bodyFormData = new FormData();
            bodyFormData.append("cmd", this.new_demand.cmd);
            bodyFormData.append("demand", JSON.stringify(this.new_demand));

            axios({
                method: "post",
                url: this.url,
                data: bodyFormData,
                headers: {"Content-Type": "multipart/form-data"},
            }).then((response) => {
                console.log(response);
                // обновляем список
                if (response.data.result) {
                    // this.get_list();
                    // this.hide_new_demand_modal();
                    this.data.updated = !this.data.updated
                    this.$alert("Операция успешно завершена", "is-success");
                    return true;
                }

                console.log(this.new_demand);
                console.log(response.data);
                this.$alert(
                    "Не удалось сохранить заявку. Попробуйте снова",
                    "is-warning"
                );
            });
        },

        
        hide_new_demand_modal: function () {
            // показать окно новой заявки
            this.new_demand.visible = false;
        },

        org_name_normalization: function () {
            // нормализация названия организации
            var org_name = this.new_demand.org_name;
            org_name = org_name.trim();
            org_name = org_name.toUpperCase();

            // убираем нечитаемые символы и ковычки
            let arr = [9, 10, 13, 39, 34, 171, 147, 148, 187, 8220, 8221];
            arr.forEach(function (e) {
                var l = String.fromCharCode(e);
                // НУЖНО СДЕЛАТЬ ЧЕРЕЗ РЕГУЛЯРНЫЕ ВЫРАЖЕНИЯ
                org_name = org_name.replace(l, "");
                org_name = org_name.replace(l, "");
                org_name = org_name.replace(l, "");
                org_name = org_name.replace(l, "");
            });

            // переносим формы собственности
            this.org_forms.forEach(function (e) {
                var string1 = e + " ";
                var string2 = org_name.slice(0, string1.length);
                if (string1 === string2) {
                    org_name = org_name.slice(string1.length) + " " + e;
                }
            });

            this.new_demand.org_name = org_name;
            this.find_original_clients()
        },
        
        find_original_clients(){
            this.isLoading = true
            this.finded_same = true
            var bodyFormData = new FormData();
            bodyFormData.append("cmd", "find_original_client");
            bodyFormData.append("name", this.new_demand.org_name);
            bodyFormData.append("email", this.new_demand.mail_adress_2);
            bodyFormData.append("phone", this.new_demand.contact_tel);
            bodyFormData.append("city", this.new_demand.org_adress);

            axios({
                method: "post",
                url: this.url,
                data: bodyFormData,
                headers: {"Content-Type": "multipart/form-data"},
            }).then((response) => {
                // обновляем список
                console.log(response.data);
                this.new_demand.origin_client = response.data
                if(response.data.length < 15 && response.data.length > 0){
                    this.isLoading = false
                    return
                }else if(response.data.length == 0){
                    this.finded_same = false
                    this.isLoading = true
                    return
                }
            });
        },

        set_client(client){
            this.new_demand.client = client.Код
            if(this.new_demand.add_new_client){
                this.new_demand.add_new_client = false
            }
        },

        set_client_empty(){
            this.new_demand.client = null
        }
    }
}
</script>

<style scoped>
    .modal-background{
        z-index: 30;
    }

    .modal_block{
        width: 60vw;
        height: 85vh;
        background: #ececec;
        padding: 10px;
        display: flex;
        flex-direction: column;
    }

    .demand_info{
        display: flex;
        flex-direction: column;
        width: 50%;
        padding: 5px;
    }

    .demand_info>.modal-paragraph-header:first-child{
        margin-top: 0px;
    }

    .demand_info_top{
        display: flex;
        flex-direction: row;
    }

    .tm_input{
        margin-bottom: 5px;
        flex-grow: unset;
    }
    .mail_recipient{
        margin-bottom: 5px;
    }

    .keywords_wrapper {
        padding: 0px;
        text-align: left;
        display: flex;
        flex-direction: column;
    }

    .same_clients {
        border-radius: 4px;
        background: white;
        border: 1px solid gray;
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        padding: 5px;
        flex-grow: 1;
        overflow: hidden;
        flex-basis: 100px;
        text-align: center;
    }

    .same_clients_list {
        border-radius: 4px;
        background: white;
        border: 1px solid gray;
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        padding: 5px;
        flex-grow: 1;
        position: relative;
        overflow: hidden;
        flex-basis: 100px;
        text-align: center;
        width: 100%;
    }

    .search_done{
        overflow: auto;
    }

    .finded_same_text{
        color: black;
        font-weight: 800;
    }
    .promt_text {
        outline: none;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        background: rgba(0, 0, 0, 0.1);
        padding: 0px 3px;
        font-size: 8pt;
        font-weight: bold;
        margin-right: 5px;
    }

    .active_client{
        background: lightgray;
    }

    .same_client_row{
        cursor: pointer;
        transition: all 0.25s ease-in-out 0s;
        width: 100%;
        text-align: left;
    }

    .tm_input{
        resize: none;
    }

    .add_new_client_opt{
        z-index: 30;
        width: 100%;
        cursor: pointer;
        text-align: left;
    }

    .button-confirm, .button-cancel{
        margin-left: 0px;
        margin-right: 0px;
    }

    .textarea_big{
        flex-grow: 1;
    }

    .rang_task>*{
        margin-right: 5px;
    }
</style>
