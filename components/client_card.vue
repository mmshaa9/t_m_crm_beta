<template>
    <!-- <div class="card_modal"> -->
        <!-- <div class="close_modal" @click="$parent.close()">
            <div class="client_card_header">Карточка клиента</div>
            Закрыть
            <b-icon pack="fas" icon="window-close" label="Закрыть" />
        </div> -->
        <div class="column_positions">
            <b-loading v-model="isLoading" :is-full-page="isFullPage"></b-loading>
            <div class="client_col">
                <div class="filter_container_global hidden_element">
                    <div class="bold head_title">Список клиентов</div>
                </div>
                <div class="client_list">
                    <div class="client_wrapper">
                        <div class="client_listed">
                            <demand-item v-for="(client, index) in clients" :client="client" :class="{
                                    text_green: client.Категория == 'ЗелАктив',
                                    text_light_green:
                                        client.Категория == 'ЗелПассив',
                                    text_red:
                                        client.Категория ==
                                        ('Забытый' || 'В разработке'),
                                    text_fade: client.Категория == 'Отказной',
                                    active_client: active_client == client.заявка_код,
                                }"
                                :key="index"
                                :all="true"
                                @click.native="load_client_data(client)">
                            </demand-item>
                        </div>
                    </div>
                </div>
            </div>
            <div class="desc_col">
                <transition-group name="fade">
                    <div
                        v-for="(info, index) in infos"
                        :key="index"
                        class="info_mass"
                    >
                        <b-field
                            label="Название"
                            horizontal
                            custom-class="desc_label"
                        >
                            <b-input
                                custom-class="gray_readonly"
                                readonly
                                expanded
                                size="is-small"
                                :value="info.Название_формат"
                                type="text"
                            ></b-input>
                        </b-field>
                        <b-field
                            label="Адрес"
                            horizontal
                            custom-class="desc_label"
                        >
                            <b-input
                                custom-class="gray_readonly"
                                expanded
                                size="is-small"
                                :value="info.Адрес"
                                readonly
                                type="text"
                                icon-pack="fa"
                                icon-right="map-marker-alt"
                                icon-right-clickable
                                @icon-right-click="open_geo(info)"
                            ></b-input>
                        </b-field>
                        <div class="demand_options_f">
                        <b-field label="Информация о заявке" custom-class="desc_label">
                            <b-input
                                type="textarea"
                                @change.native="edit_client_info(info)"
                                v-model="info.информация"
                                rows="10"
                                placeholder="Информация о клиенте"
                            >
                            </b-input>
                        </b-field>
                        <div class="field_decline_change">
                            <div class="change_manager">
                                <div class="stage_info">   
                                    <span class="span_desc_bot">Текущий этап</span>
                                    <b-input
                                        size="is-small"
                                        expanded
                                        readonly
                                        custom-class="gray_readonly stage_demand"
                                        v-model="cur_client_marketing_stage"
                                    ></b-input>
                                </div>
                                <b-button size="is-small" @click="open_orders">
                                    Заказы
                                </b-button>
                                <b-button :disabled="manager.works" size="is-small" @click="get_demand_to_self">
                                    Забрать заявку себе
                                </b-button>
                            </div>
                        </div>
                    </div>
                    </div>
                </transition-group>
                <div class="contacts">
                    <b-field label="Контакты" custom-class="desc_label" horizontal>
                        <b-field position="is-right" grouped>
                            <!-- <b-button size ="is-small" @click="add_contact_modal = true">Добавить контакт</b-button> -->
                        </b-field>
                    </b-field>
                    <div class="contact_wrapper">
                        <transition-group name="fade">
                            <div
                                class="contact_card"
                                v-for="(contact, index) in contacts"
                                :key="index"
                            >
                                <div class="note_row">
                                    <b-field
                                        label="ФИО"
                                        horizontal
                                        custom-class="contact_label"
                                        class="input_space"
                                    >
                                        <b-input
                                            type="text"
                                            size="is-small"
                                            readonly
                                            :value="contact.ФИО"
                                        ></b-input>
                                    </b-field>

                                    <b-field
                                        horizontal
                                        label="Должность"
                                        custom-class="contact_label"
                                        class="input_space"
                                    >
                                        <b-input
                                            type="text"
                                            readonly
                                            size="is-small"
                                            :value="contact.Должность"
                                        ></b-input>
                                    </b-field>
                                    <b-field
                                        horizontal
                                        label="Телефон №"
                                        custom-class="contact_label"
                                        class="input_space"
                                    >
                                        <b-input
                                            type="text"
                                            readonly
                                            size="is-small"
                                            :value="contact.Телефон"
                                        ></b-input>
                                    </b-field>
                                    <b-field
                                        horizontal
                                        label="E-mail"
                                        custom-class="contact_label"
                                        class="input_space"
                                    >
                                        <b-input
                                            type="text"
                                            readonly
                                            size="is-small"
                                            :value="contact.Почта"
                                            icon-right="email"
                                            icon-right-clickable
                                            @icon-right-click="
                                                open_mail_to(contact)
                                            "
                                        ></b-input>
                                    </b-field>
                                    <!-- <b-field position="is-right" grouped>
                                        <b-button type="is-text" icon-pack="fas" size="is-small" icon-right="trash-alt" @click="delete_client_contact(contact)">Удалить контакт</b-button>
                                    </b-field> -->
                                </div>
                            </div>
                        </transition-group>
                    </div>
                </div>
            </div>
            <div class="notes_col">
                <section>
                    <b-field grouped position="is-right">
                        <p class="control">
                            <b-button
                                class="button_margin"
                                label="Добавить заметку"
                                size="is-small"
                                type="is-warning"
                                @click="note_create_visible = true"
                            ></b-button>
                        </p>
                        <b-input
                            type="search"
                            placeholder="Поиск"
                            size="is-small"
                            expanded
                            v-model="filter_text_note"
                            @input="set_filter_text_note"
                        ></b-input>
                    </b-field>
                </section>
                <div class="notes_list">
                    <div class="button_header">
                        <div
                            class="button_top notes_button_top"
                            v-for="(tag, index) in note_tags"
                            :key="index"
                            @click="set_note_filter_tag(tag)"
                            :class="{ bold_tag: filter_note == tag }"
                        >{{tag}}</div>
                    </div>
                    <div class="notes_wrapper">
                        <transition-group name="fade">
                            <div
                                class="note_row"
                                v-for="(note, index) in notes"
                                :key="index"
                                :class="{
                                    back_green:
                                        note.note_succ == -1 &&
                                        note.note_event == 1 &&
                                        note.note_res == 4,
                                    back_red:
                                        note.note_succ == false &&
                                        note.note_event == 1 &&
                                        note.note_res == 4,
                                }"
                            >
                                <div class="note_head">
                                    <div
                                        v-if="note.worker_id == user"
                                        class="fas fa-pen"
                                        @click="edit_note(note)"
                                    ></div>
                                    <div
                                        v-if="
                                            (note.attach_count == '0' ||
                                                note.attach_count == 0) &&
                                            note.worker_id == user
                                        "
                                        class="fas fa-trash-alt"
                                        @click="delete_note_confirm(note)"
                                    ></div>
                                    <div
                                        v-if="note.note_event == '1'"
                                        class="message_date"
                                    >
                                        {{
                                            note.date_note +
                                            " " +
                                            note.first_time_note +
                                            " (от " +
                                            note.note_created +
                                            "), " +
                                            note.worker_name
                                        }}
                                    </div>
                                    <div v-else class="message_date">
                                        {{
                                            note.note_created +
                                            " " +
                                            note.first_time_note +
                                            ", " +
                                            note.worker_name
                                        }}
                                    </div>
                                </div>
                                <div class="note_text">
                                    <spoiler
                                        :key="note.note_text"
                                        :length="400"
                                        :text="note.note_text"
                                        show_text="Показать полностью"
                                    ></spoiler>
                                </div>
                                <div class="note_head">
                                    <my-attachments
                                        :user="user"
                                        :data="{
                                            empty: note.attach_count == 0,
                                            items: note.attachments || [],
                                        }"
                                        @load-attachments="
                                            load_attachments(note)
                                        "
                                        @hide-attachments="
                                            hide_attachments(note)
                                        "
                                        @open-attachment="
                                            open_attachment($event, note)
                                        "
                                        @add-attachment="
                                            add_attachment_to_note(note)
                                        "
                                        @del-attachment="
                                            delete_attachment_on_note(
                                                $event,
                                                note
                                            )
                                        "
                                    ></my-attachments>
                                </div>
                            </div>
                        </transition-group>
                    </div>
                    <b-modal v-model="edit_note_visible" :width="550">
                        <div class="modal_card">
                            <div class="header_modal">
                                Редактировать заметку
                            </div>
                            <b-field label="Сообщение">
                                <b-input
                                    maxlength="200"
                                    v-model="edited_note_text"
                                    type="textarea"
                                ></b-input>
                            </b-field>
                            <div>
                                <b-field grouped addons position="is-right">
                                    <b-input
                                        size="is-small"
                                        type="date"
                                        v-model="edited_note_date"
                                        :value="edited_note_date"
                                    ></b-input>
                                    <b-input
                                        size="is-small"
                                        type="time"
                                        v-model="edited_note_time"
                                    ></b-input>
                                    <b-button
                                        size="is-small"
                                        label="Сохранить"
                                        type="is-warning"
                                        @click="save_note"
                                    ></b-button>
                                    <b-button
                                        size="is-small"
                                        class="cancel_button"
                                        label="Отмена"
                                        @click="edit_note_visible = false"
                                    ></b-button>
                                </b-field>
                            </div>
                        </div>
                    </b-modal>

                    <b-modal v-model="note_create_visible" :width="550">
                        <div class="modal_card">
                            <div class="header_modal">Добавить заметку</div>
                            <b-field label="Сообщение">
                                <b-input
                                    maxlength="200"
                                    v-model="note_text"
                                    type="textarea"
                                ></b-input>
                            </b-field>
                            <div>
                                <b-field grouped addons position="is-right">
                                    <b-input
                                        size="is-small"
                                        type="date"
                                        placeholder="Дата:"
                                        v-model="note_date"
                                    ></b-input>
                                    <b-input
                                        size="is-small"
                                        type="time"
                                        placeholder="Время:"
                                        v-model="note_time"
                                    ></b-input>
                                    <b-button
                                        size="is-small"
                                        label="Отправить заметку"
                                        @click="add_note_with_timestamp"
                                        type="is-warning"
                                    ></b-button>
                                    <b-button
                                        size="is-small"
                                        class="cancel_button"
                                        label="Отмена"
                                        @click="note_create_visible = false"
                                    ></b-button>
                                </b-field>
                            </div>
                        </div>
                    </b-modal>
                    <b-field> </b-field>
                </div>
            </div>
            <select-box
                :data="orders"
                @select_item="select_order($event)"
                @confirm_action="open_order($event)"
            ></select-box>
        </div>
    <!-- </div> -->
</template>
<script>
import axios from "axios";
import select_box from "@/components/select_box.vue?t=";
import my_attachments from "@/components/my_attachments.vue?t=";
import wait_indicator from "@/components/wait_indicator.vue?t=";
import spoiler from "@/components/spoiler.vue?t=";
import { mapActions } from 'vuex'
import * as my_f from "@/my";
import demand_item from "@/components/demand_item.vue"

export default {
    directives: {
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

    components: {
        "my-attachments": my_attachments,
        "wait-indicator": wait_indicator,
        "select-box": select_box,
        'demand-item': demand_item,
        'spoiler': spoiler,
    },
    props: ["data"],

    data() {
        return {
            isLoading: false,
            isFullPage: false,
            clients: [],
            active_client: "",
            cur_client: this.data.cur_client,
            // cur_client:'',
            cur_client_decline: "",
            cur_client_tech_sup: "",
            cur_client_marketing_stage: "",
            cur_demand:'',

            filter_client: "Все",
            contacts: [],
            notes: [],
            infos: [
                {
                    Название_формат: "",
                    Адрес: "",
                    информация: "",
                },
            ],
            adress_list: [],
            adress: "",

            rang_list: [],
            edit_client_name_modal: false,
            add_contact_modal: false,
            edit_contact_modal: false,
            new_contact_name: "",
            new_contact_rang: "",
            new_contact_number: "",
            new_contact_mail: "",

            company_types: ["ООО", "ОАО", "ЗАО", "ПАО", "АО", "ИП"],
            client_type: "",
            client_name: "",
            client_info: "",
            client_adress: "",

            decline_client_modal: false,
            decline_reasons_list: [
                "Нашел дешевле",
                "Не нашел нужного",
                "Трижды не ответил на звонок",
                "Другое",
            ],
            decline_reason_type: "",
            decline_reason_comment: "",

            manager_tag: "",
            // notes_templates: ['Не дозвонился', 'Нет ЛПР', 'Отправлено инфо письмо', 'Отправлен счет'],
            // manager_tags: ['Заявка (ЛИД квалифицирован)', 'Интерес проявлен/ТЗ получено', 'КП выставлено', 'КП презентовано', 'Положительное решение принято', 'Договор подписан, счет выставлен', 'Оплата произведена'],
            stages: [],
            stage_active: false,
            stage_name: "",
            KP_price: "",
            set_price: false,

            note_text: "",
            note_date: "",
            note_time: "",
            note_create_visible: false,
            edit_note_visible: false,
            edited_note_text: "",
            edited_note_date: "",
            edited_note_time: "",
            edited_note_res: "",
            edited_check: false,
            current_attachments: "",
            current_note_id: "",
            current_note_res: "",

            orders_need: ["Да", "Нет"],
            manager_orders_needed: "",

            managers: [],
            give_client_modal: false,
            manager_new: "",
            manager_new_id: "",

            tags: ["Все", "С заказами", "В разработке", "Отказные"],
            note_tags: ["Заметки по клиенту", "Заметки по заявке"],
            filter_note: "Заметки по клиенту",
            url: this.$store.getters.api_url_new,
            filter_text: "",
            filter_text_note: "",
            filter_text_note_memory: {value: undefined},
            filter_text_memory: {value: undefined},
            user: "",
            modal_visibly_create: false,
            modal_set_stage: false,
            wait_indicator_visible: false,
            selected: null,
            loading: false,
            orders: {
                isactive: false,
                items: [],
                width: 500,
                header_modal: "Заказы",
                filter: false,
                active_item: "",
                min_height: 300,
                filter_text: "",
            },
            manager:{
                works:null,
                id:null
            }
        };
    },

    computed: {
        filteredList_adress: function () {
            return this.adress_list.filter((option) => {
                return (
                    option.Адрес
                        .toString()
                        .toLowerCase()
                        .indexOf(this.client_adress.toLowerCase()) >= 0
                );
            });
        },

        filteredList_rangs: function () {
            return this.rang_list.filter((option) => {
                return (
                    option.Выражение1
                        .toString()
                        .toLowerCase()
                        .indexOf(this.new_contact_rang.toLowerCase()) >= 0
                );
            });
        },

        dsk_visible: function (client) {
            return {
                visible_dsk:
                    client.Категория == "В разработке" ||
                    client.Категория == "Забытый" ||
                    (client.Категория == "ЗелПассив" &&
                        client.Последнее_событие != "??.??.????"),
                unviisible_dsk:
                    client.Категория == "Отказной" ||
                    (client.Категория == "ЗелПассив" &&
                        client.Последнее_событие == "??.??.????"),
            };
        },
    },

    mounted: function () {
        this.user = this.$store.getters.USER_ID;

        this.load_client_data();
    },

    methods: {
        ...mapActions([
            'confirm'
        ]),
        
        load_client_data: function (client = null) {
            this.isLoading = true
            this.wait_indicator_visible = true;
            if(client != null){
                this.cur_demand = client.заявка_код;
            }
            axios
                .get(this.url, {
                    params: {
                        cmd: "get_client_demands",
                        client_id: this.cur_client,
                        demand_id: this.cur_demand,
                        filter: this.filter_note,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    this.isLoading = false
                    if (response.data.length == 0) {
                        this.$alert("Данные не найдены", "is-warning");
                    } else {
                        this.wait_indicator_visible = false;
                        this.format_all_data(response);
                    }
                    console.log(response.data);
                });
        },

        format_all_data: function (meta) {
            this.contacts = meta.data.contacts;
            this.clients = meta.data.clients;
            this.cur_client = meta.data.client.Код;
            this.cur_demand = meta.data.client.заявка_код
            this.cur_client_tech_sup = meta.data.client.ттс;
            this.cur_client_decline = meta.data.client.отказ;
            this.active_client = this.cur_demand;
            this.infos = meta.data.infos;
            this.notes = meta.data.notes;
            if (meta.data.last_res.result == "-empty") {
                this.cur_client_marketing_stage = "ЗАЯВКА ПОЛУЧЕНА";
            } else {
                this.cur_client_marketing_stage = meta.data.last_res.result;
            }
            this.stages = meta.data.stages;
            this.manager.works = meta.data.client.менеджер_работает
        },

        load_adress_list: function () {
            axios
                .get(this.url, {
                    params: {cmd: "get_clients_adress", t: new Date()},
                })
                .then((response) => {
                    // console.log(response.data)
                    this.adress_list = response.data;
                });
        },

        load_client_rangs: function () {
            axios
                .get(this.url, {
                    params: {cmd: "get_clients_rangs", t: new Date()},
                })
                .then((response) => {
                    // console.log(response.data)
                    this.rang_list = response.data;
                });
        },

        load_last_res: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "get_client_last_res",
                        client_id: this.cur_client,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    console.log(response.data);
                    if (response.data.result == "-empty") {
                        this.cur_client_marketing_stage = "ЗАЯВКА ПОЛУЧЕНА";
                    } else {
                        this.cur_client_marketing_stage = response.data.result;
                    }
                });
        },

        edit_client_name: function (info) {
            this.edit_client_name_modal = true;
            this.client_name = info.Название;
            this.client_adress = info.Адрес;
            this.client_info = info.информация;
        },

        edit_client_info: function (info) {
            console.log(info.информация);
            axios
                .get(this.url, {
                    params: {
                        cmd: "marketing_edit_client_info",
                        client_id: this.cur_client,
                        client_info: info.информация,
                        cu: this.user,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    console.log(response.data);
                    if (response.data.result == "-ok") {
                        this.$alert(
                            "Информация по клиенту обновлена",
                            "is-success"
                        );
                        this.infos = response.data.info;
                    } else {
                        this.$alert(
                            "Не удалось обновить информацию по клиенту, проверьте данные.",
                            "is-danger"
                        );
                    }
                });
        },

        confirm_back_client_to_work: function () {
            this.$buefy.dialog.confirm({
                title: "",
                message: "Вы действительно хотите вернуть клиента в работу?",
                cancelText: "Да",
                confirmText: "Нет",
                closeOnConfirm: true,
                type: "",
                focusOn: "cancel",
                trapFocus: false,
                onCancel: () => this.back_client_to_work(),
                onConfirm: () => {
                    return;
                },
            });
        },

        add_note_with_timestamp: function () {
            var format_note_time;
            var format_note_date;
            if (this.note_time != "") {
                format_note_time = this.note_time;
            }
            if (this.note_date != "") {
                format_note_date = my_f.date_format(
                    new Date(this.note_date),
                    "dd.mm.yyyy"
                );
            }
            axios
                .get(this.url, {
                    params: {
                        cmd: "add_note_with_timestamp",
                        demand: this.cur_demand,
                        filter: this.filter_note,
                        cu: this.user,
                        par: this.filter_client,
                        client_id: this.cur_client,
                        user: this.user,
                        note_text: this.note_text,
                        note_date: format_note_date,
                        note_time: format_note_time,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    console.log(response.data);
                    if (response.data.result == "-ok") {
                        // this.loadData()
                        // var cur_client = { x: 0, Код: this.cur_client}
                        // this.load_notes(cur_client)
                        this.notes = response.data.notes;
                        this.load_client_data()
                        this.note_time = "";
                        this.note_date = "";
                        this.note_text = "";
                        this.note_create_visible = false;
                        this.$alert("Заметка добавлена", "is-success");
                    } else {
                        this.$alert(
                            "Не удалось добавить заметку",
                            "is-warning"
                        );
                    }
                });
        },

        edit_note: function (note) {
            this.edit_note_visible = true;
            this.current_note_id = note.id;
            this.edited_note_text = note.note_text;
            this.edited_note_date = my_f.date_format(
                new Date(note.note_create_main),
                "yyyy-mm-dd"
            );
            this.edited_note_time = note.first_time_note;
            this.current_note_res = note.note_res;
        },

        save_note: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "edit_marketing_notes",
                        cu: this.user,
                        par: this.filter_client,
                        filter: this.filter_note,
                        client_id: this.cur_client,
                        demand_id: this.cur_demand,
                        note_id: this.current_note_id,
                        updated_date: this.edited_note_date,
                        updated_time: this.edited_note_time,
                        updated_text: this.edited_note_text,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    console.log(response.data);
                    if (response.data.result == "-ok") {
                        this.notes = response.data.notes;
                        this.clients = response.data.clients.clients;
                        this.$alert("Заметка изменена", "is-success");
                        this.edit_note_visible = false;
                    } else {
                        this.$alert(
                            "Не удалось обновить заметку. Попробуйте еще раз",
                            "is-warning"
                        );
                    }
                    // var cur_client = { x: 0, Код: this.cur_client}
                    // this.load_notes(cur_client)
                });
        },

        delete_note_confirm: function (note) {
            this.confirm({
                text: "Вы действительно хотите удалить заметку?",
                confirm_action:()=>{this.delete_note(note)}
            })
        },

        delete_note: function (note) {
            this.current_note_id = note.id;
            console.log(this.current_note_id);
            // return
            axios
                .get(this.url, {
                    params: {
                        cmd: "delete_marketing_notes",
                        cu: this.user,
                        par: this.filter_client,
                        filter: this.filter_note,
                        client_id: this.cur_client,
                        demand_id: this.cur_demand,
                        note_id: this.current_note_id,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    if (response.data.result == "-ok") {
                        this.$alert("Заметка удалена", "is-success");
                        this.notes = response.data.notes;
                        this.edit_note_visible = false;
                    } else {
                        this.$alert(
                            "Не удалось удалить заметку, попробуйте снова",
                            "is-warning"
                        );
                    }
                });
        },

        load_notes2: function () {
            axios
                // var cur_client = { x: 0, Код: this.cur_client}
                .get(this.url, {
                    params: {
                        cmd: "get_notes_list1",
                        client_id: this.cur_client,
                        demand_id: this.cur_demand,
                        filter: this.filter_note,
                        filter_text_note: this.filter_text_note,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    console.log(response.data);
                    this.notes = response.data;
                    this.notes.forEach((e) => {
                        e.hidden_text = e.note_text.substring(0, 399);
                    });
                });
        },

        load_attachments: function (note) {
            // this.$set(note, 'attachments',[])
            axios
                .get(this.url, {
                    params: {
                        cmd: "client_note_get_attachment_list",
                        note_id: note.id,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    // console.log(response.data)
                    this.$set(note, "attachments", response.data);
                    // note.attachments = response.data
                    // this.current_attachments = response.data
                    // this.attach = response.data
                    // this.current_note_id = note.attachment.Код
                });
        },

        hide_attachments: function (note) {
            //скрываем вложения
            console.log("hide");
            note.attachments = [];
        },

        open_attachment: function (attachment, note) {
            //открываем вложение и скачиваем
            console.log("open");
            console.log(note);
            // отрабатываем событие в браузере
            console.log(attachment.url2);
            my_f.file_operation('open', attachment.url2);
        },

        add_attachment_to_note: function (note, data) {
            my_f.selectFile((files) => {
                // загружаем файлы по одному
                for (var i = 0; i < files.length; i++) {
                    // загружаем файлы по одному
                    my_f.uploadFile(
                        {
                            cmd: "marketing_note_add_attachment",
                            note_id: note.id,
                            userfile: files[i],
                            user: this.user,
                        },
                        () => {
                            this.load_attachments(note);
                        },
                        this.url
                    );
                }
            });
        },

        delete_attachment_on_note: function (id, note) {
            console.log(id);
            console.log(note);
            axios
                .get(this.url, {
                    params: {
                        cmd: "delete_attach_from_note",
                        attachment_id: id,
                        note_id: note.id,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    if (response.data.result == "-ok") {
                        console.log(response.data);
                        this.$alert("Вложение удалено", "is-success");
                        if (response.data.attach.length == 0) {
                            note.attachments = [];
                            note.attach_count = 0;
                        } else {
                            note.attachments = response.data.attach;
                        }
                    } else {
                        this.$alert(
                            "Не удалось удалить вложение, попробуйте снова",
                            "is-warning"
                        );
                    }
                });
        },

        set_filter_text: function () {
            //app.filter2 = 0
            my_f.throttled_input(
                this.filter_text,
                this.filter_text_memory,
                this.loadData,
                1000
            );
            //this.loadData()
        },

        load_managers_list: function () {
            axios
                .get(this.url, {
                    params: {cmd: "marketing_get_managers_list", t: new Date()},
                })
                .then((response) => {
                    this.managers = response.data;
                });
        },

        manager_orders_need: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "marketing_manager_orders_need",
                        user: this.user,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    this.manager_orders_needed = response.data;
                    if (this.manager_orders_needed[0].orders_need == 0) {
                        this.manager_orders_needed = this.orders_need[1];
                    } else {
                        this.manager_orders_needed = this.orders_need[0];
                    }
                });
        },

        set_manager_orders_need: function () {
            var set_need;
            if (this.manager_orders_needed == "Да") {
                set_need = true;
            } else {
                set_need = false;
            }
            axios
                .get(this.url, {
                    params: {
                        cmd: "set_manager_orders_need",
                        user: this.user,
                        set_need: set_need,
                        t: new Date(),
                    },
                })
                .then(function (response) {
                    if (response.data.result == "-ok") {
                        this.$alert("Статус обновлен", "is-success");
                    } else {
                        this.$alert(
                            "не удалось обновить статус, попробуйте позднее",
                            "is-warning"
                        );
                    }
                    // note.attachments = response.data
                });
        },

        load_contacts2: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "get_contact_list1",
                        client_id: this.cur_client,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    this.contacts = response.data;
                });
        },

        add_client_open: function () {
            this.add_contact_modal = true;
            this.new_contact_name = "";
            this.new_contact_rang = "";
            this.new_contact_number = "";
            this.new_contact_mail = "";
        },

        add_client_contact: function (par) {
            if (
                !my_f.is_Email(this.new_contact_mail) &&
                this.new_contact_mail != ""
            ) {
                this.$alert(
                    "Укажите корректный адрес клиента и попробуйте снова",
                    "is-warning"
                );
                return false;
            }
            if (this.new_contact_name == "")
                return this.$alert("Укажите имя контакта", "is-warning");
            console.log(par);
            if (par == 1) {
                axios
                    .get(this.url, {
                        params: {
                            cmd: "add_client_contact",
                            user: this.user,
                            name: this.new_contact_name,
                            rang: this.new_contact_rang,
                            number: this.new_contact_number,
                            mail: this.new_contact_mail,
                            client_id: this.cur_client,
                            t: new Date(),
                        },
                    })
                    .then((response) => {
                        // console.log(response.data)
                        if (response.data.result == "-ok") {
                            this.$alert("Контакт добавлен", "is-success");
                            this.load_contacts2();
                            this.add_contact_modal = false;
                            this.new_contact_name = "";
                            this.new_contact_rang = "";
                            this.new_contact_number = "";
                            this.new_contact_mail = "";
                        } else {
                            this.$alert(
                                "Не удалось добавить контакт, попробуйте позже",
                                "is-warning"
                            );
                        }
                    });
            } else {
                // return console.log('heh')
                axios
                    .get(this.url, {
                        params: {
                            cmd: "edit_client_contact",
                            user: this.user,
                            name: this.new_contact_name,
                            rang: this.new_contact_rang,
                            number: this.new_contact_number,
                            mail: this.new_contact_mail,
                            client_id: this.cur_client,
                            t: new Date(),
                        },
                    })
                    .then((response) => {
                        console.log(response.data);
                        if (response.data.result == "-ok") {
                            this.$alert("Контакт обновлен", "is-success");
                            this.load_contacts2();
                            this.edit_contact_modal = false;
                            this.new_contact_name = "";
                            this.new_contact_rang = "";
                            this.new_contact_number = "";
                            this.new_contact_mail = "";
                        } else {
                            this.$alert(
                                "Не удалось добавить контакт, попробуйте позже",
                                "is-warning"
                            );
                        }
                    });
            }
        },

        edit_client_contact: function (contact) {
            this.edit_contact_modal = true;
            this.new_contact_name = contact.ФИО;
            if (contact.Должность == null) {
                contact.Должность = "";
            }
            this.new_contact_rang = contact.Должность;
            this.new_contact_number = contact.Телефон;
            this.new_contact_mail = contact.Почта;
        },

        delete_client_contact: function (contact) {
            console.log(contact.Код);
            axios
                .get(this.url, {
                    params: {
                        cmd: "marketing_delete_contact",
                        contact_id: contact.Код,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    if (response.data.result == "-ok") {
                        this.$alert("Контакт удален", "is-success");
                        this.load_contacts2();
                    } else {
                        this.$alert("Не удалось удалить контакт", "is-warning");
                    }
                });
        },

        open_mail_to: function (contact) {
            if (contact.Почта != "")
                return window.open("mailto:" + contact.Почта);
        },

        open_geo(info) {
            if (info.Адрес == "[не указан]")
                return this.$alert("Не указан адрес");
            window.open(
                "http://maps.yandex.ru/?source=morda&text=" + info.Адрес
            );
        },

        get_client_marketing_stage: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "get_client_marketing_stage_list",
                        client_id: this.cur_client,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    this.stages = response.data;
                });
        },

        open_stage_markup: function () {
            this.modal_set_stage = true;
            this.stage_name = "";
            this.KP_price = "";
            this.set_price = false;
        },

        choose_stage: function (stage) {
            console.log(stage.Код);
            this.stage_name = stage.Этап;
            if (stage.Код == 2) {
                this.set_price = true;
            } else {
                this.set_price = false;
            }
        },

        set_client_marketing_stage: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "set_client_marketing_stage",
                        user: this.user,
                        client_id: this.cur_client,
                        stage_name: this.stage_name,
                        stage_price: this.KP_price,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    console.log(response.data);
                    if (response.data.result == "-ok") {
                        this.load_notes2();
                        this.load_last_res();
                        this.stage_name = "";
                        this.KP_price = "";
                        this.set_price = false;
                        this.modal_set_stage = false;
                        this.$alert("Этап продажи отмечен", "is-success");
                    } else {
                        this.$alert(
                            "Не удалось обновить информацию, попробуйте еще раз",
                            "is-warning"
                        );
                    }
                });
        },

        set_filter_text_note: function () {
            //app.filter2 = 0

            my_f.throttled_input(
                this.filter_text_note,
                this.filter_text_note_memory,
                this.load_notes2,
                1000
            );
            //this.loadData()
        },

        set_manager_tag: function (par) {
            this.manager_tag = par;
            this.note_text = this.manager_tag;
            // console.log(this.manager_tag)
        },

        set_filter_tag: function (par) {
            this.filter_client = par;
            this.loadData();
        },

        show_all_note_text: function (note) {
            note.hidden_text = note.note_text;
        },

        hide_text: function (note) {
            note.hidden_text = note.note_text.substring(0, 399);
        },

        open_orders: function () {
            axios
                .get(this.url, {
                    params: {cmd: "get_client_orders", client: this.cur_client},
                })
                .then((response) => {
                    console.log(response.data);
                    if (response.data.length == 0) {
                        this.$alert(
                            "У данного клиента отсутствуют заказы",
                            "is-warning"
                        );
                    } else {
                        this.orders.items = response.data;
                        this.orders.isactive = true;
                        this.orders.active_item = "";
                    }
                });
        },

        select_order: function (order) {
            this.orders.active_item = order.name;
        },

        open_order: function (order) {
            this.$store.dispatch("SET_MARKET_ORDER", order.Код);
            this.$store.dispatch("SET_MARKET_CHAT", order.беседа_код);
            this.$store.dispatch("SET_PREVIOUS_PAGE", "orders");
            this.orders.isactive = false;
            let card_order = this.$router.resolve({
                name: "marketing_order_card",
            });
            window.open(card_order.href, "_blank");
        },

        set_note_filter_tag(par){
            this.filter_note = par
            this.load_notes2()
        },

        get_demand_to_self(){
            this.isLoading = true
            var bodyFormData = new FormData()
            bodyFormData.append('cmd', 'marketing_take_demand_to_user')
            bodyFormData.append('client_id', this.cur_client)
            bodyFormData.append('demand_id', this.cur_demand)
            bodyFormData.append('user', this.user)
            
            axios({
                method: 'post',
                url: this.url,
                data: bodyFormData,
                headers: {'Content-Type': 'multipart/form-data' }
                }) 
            .then((response)=>{    
                this.isLoading = false
                if(response.data.result == "-ok"){
                    this.$alert('Операция успешно завершена','is-success')
                    this.format_all_data(response.data)
                }else{
                    this.$alert('Не удалось завершить операцию, попробуйте позже','is-warning')
                }
                console.log(response.data) 
                
            })
        }
    },
};
</script>

<style scoped>
.card_modal {
    display: flex;
    flex-direction: column;
    
    border: 5px solid rgb(36, 36, 36);
}

.column_positions {
    display: flex;
    padding-top: 5px;
    width: 80vw;
    height: 85vh;
    overflow: hidden;
    position: relative;
}

.app_flex {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
}

.filter_container_global {
    align-items: center;
}

.filter_container_global > * {
    margin-right: 0px;
    padding-right: 0px;
}

/* .filter_input_global {
    height: 20px;
} */

.head_title {
    margin-left: 0;
    padding-left: 0;
}

.content_list {
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;

    width: 100%;
    height: 100%;
    overflow: hidden;
}

.client_col {
    display: flex;
    flex-direction: column;
    width: 500px;
    margin-left: 10px;
    margin-right: 10px;
    margin-bottom: 5px;
}

.client_list {
    /* height: calc(100% - 6px); */
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    overflow: auto;
}

.client_wrapper {
    height: 100%;
    overflow-y: auto;
    border: 1px solid lightgray;
    box-shadow: rgb(0 0 0) 0px 0px 1px inset;
    background: antiquewhite;
}

.client_listed {
    padding: 5px;
}

.button_header {
    display: flex;
}

.button_top {
    border-top: 1px solid lightgray;
    border-left: 1px solid lightgray;
    border-image: initial;
    border-top-right-radius: 10px;
    border-bottom: none;
    border-right: none;
    width: 118px;
    padding: 5px;
    text-align: center;
    cursor: pointer;
    white-space: pre;
    transition: all 0.25s ease-in-out;
}

.button_top:hover {
    /* background-color: #e0eefd; */
    /* background-color: transparent; */
    /* color: #0d68ce; */
}

.button_top:last-child {
    border-right: 1px solid lightgray;
}

.bold_tag {
    /* font-weight: bold; */
    background-color: #ececec;
    text-decoration: underline;
    /* color:brown; */
}

.client_row {
    min-height: 40px;
    margin-bottom: 5px;
    background: white;
    /* color:brown; */
    padding-left: 5px;
    padding-right: 5px;
    border-radius: 5px;
    box-shadow: rgb(0 0 0 / 12%) 0px 1px 3px, rgb(0 0 0 / 24%) 0px 1px 2px;
    transition: all 0.25s ease-in-out;
}

.client_name {
    display: flex;
    justify-content: space-between;
    cursor: pointer;
}

.client_row:hover {
    opacity: 1;
    cursor: pointer;
    background-color: rgb(238, 238, 238);
}

.client_adress_last_act {
    margin-right: 5px;
    font-weight: normal;
    display: flex;
    justify-content: space-between;
}

.last_date {
    font-weight: bold;
}
/* 
.client_row:nth-child(odd){
    background-color: white;
} */

.desc_col {
    display: flex;
    flex-direction: column;
    width: 46%;
    /* height: calc(100% - 10px); */
    /* height: 100%; */
    overflow: hidden;
}

>>> .desc_label {
    color: gray;
    font-weight: 400;
    margin: 1px;
    white-space: nowrap;
    flex-grow: 0.7;
}

.desc_name {
    margin-bottom: 5px;
}

.desc_head {
    display: flex;
    margin: 5px 0px 5px 0px;
}

.span_desc {
    width: 70px;
}

.field_decline_change {
    display: flex;
    flex-grow: 1;
}

>>> .field_centred {
    align-items: center;
}

.change_manager {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
}

.change_manager>*{
    margin-right: 0px;
}

.change_manager>*:last-child{
    margin-bottom: 0px;
}

.desc_changes {
    margin-top: 15px;
    display: flex;
    align-items: center;
    justify-content: flex-end;
}

.desc_info {
    display: flex;
    flex-direction: column;
}

textarea {
    resize: none;
    /* box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24); */
}

.info_text {
}

.contacts {
    margin: 10px 0px 5px 0px;
    min-height: 212px;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    flex-grow: 1;
}

.contact_head {
    /* margin-bottom: 15px; */
}

.contact_wrapper {
    background: antiquewhite;
    overflow-y: auto;
    padding: 5px;
    height: 185px;
    box-shadow: rgb(0 0 0) 0px 0px 1px inset;
    flex-grow: 1;
}

.contact_card {
    width: 50%;
}
>>> .contact_label {
    width: 70px;
    color: gray;
    font-weight: 400;
    font-size: 13px;
}

.contact_table {
    border: 1px solid gray;
    background-color: white;
}

.contact_row {
    display: flex;
    border-bottom: 1px solid gray;
}

.contact_row:last-child {
    border-bottom: none;
}

.contact_col {
    min-width: 100px;
    border-right: 1px solid gray;
    padding: 5px;
}

.contact_del {
    cursor: pointer;
    text-align: end;
    font-size: 10pt;
}

.give_client_field {
    display: flex;
    align-items: center;
    justify-content: flex-end;
}

.span_desc_bot {
    width: 140px;
}

.border {
    border: none;
    margin: 0;
    background: none;
    width: 100%;
}

.button_global {
    width: 130px;
    margin-left: 0px;
}

.notes_col {
    display: flex;
    flex-direction: column;
    width: 500px;
    /* height: 100%; */
    height: calc(100% - 5px);
    margin-left: 10px;
    margin-right: 10px;
}

.notes_list {
    display: flex;
    flex-direction: column;
    height: calc(100% - 40px);
}

.notes_wrapper {
    border: 1px solid lightgray;
    box-shadow: rgb(0 0 0) 0px 0px 1px inset;
    height: 100%;
    padding: 5px;
    overflow: auto;
    background: antiquewhite;
}

.note_row {
    background-color: white;
    border-radius: 5px;
    padding: 5px;
    margin-bottom: 5px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}

.note_head {
    display: flex;
    align-items: center;
    padding: 2px;
}

.note_head > div {
    padding: 5px;
}

.button_status {
    display: flex;
    margin-top: 5px;
    margin-bottom: 5px;
    justify-content: space-between;
}

.button_status > * {
    padding: 5px;
    margin: 5px;
    font-size: 8pt;
    margin-left: 0px;
    min-width: 100px;
    text-align: center;
}

.button_status > div:last-child {
    margin-right: 0px;
}

.message_date {
    font-weight: bold;
}

.note_text_area {
    resize: none;
    height: 70px;
}

.note_time_date {
    display: flex;
    align-items: center;
    margin-top: 10px;
}

.add_note {
    border: 1px solid gray;
    background-color: orange;
    color: white;
    width: 85px;
    text-align: center;
}

.note_time_date > * {
    margin-right: 10px;
}

.note_time_date > div:last-child {
    margin-left: auto;
    margin-right: 0;
}

.modal_card {
    background-color: #ececec;
    padding: 10px;
    border: 1px solid black;
    border-radius: 10px;
}

>>> .label {
    text-align: left !important;
}

.header {
    text-align: center;
    font-size: 20px;
    margin: 10px;
    margin-top: 0;
    padding: 3px;
}

.field:first-child {
    /* flex-grow: 0 !important; */
}

>>> .field:not(:last-child) {
    margin-bottom: unset;
}

>>> .is-grouped-right {
    align-items: center;
}

.back_green {
    background-color: #effaf5;
    color: #257953;
}

.back_red {
    background-color: #feecf0;
    color: #cc0f35;
}

.text_green {
    color: #257953;
}

.text_light_green {
    color: #69cea0;
}

.text_red {
    color: #cc0f35;
}

.text_fade {
    opacity: 0.7;
}

>>>.btn {
    background: unset !important;
}

.field_edit_note {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
}

.heh {
    width: 110px;
}

::-webkit-scrollbar {
    width: 10px;
    opacity: 0;
}

::-webkit-scrollbar-thumb {
    border-width: 1px 1px 1px 2px;
    border-radius: 25px;
    border-color: rgb(161, 161, 161);
    background-color: rgb(161, 161, 161);
}

::-webkit-scrollbar-thumb:hover {
    border-width: 1px 1px 1px 2px;
    border-color: rgb(161, 161, 161);
    background-color: rgb(161, 161, 161);
}

::-webkit-scrollbar-track {
    border-width: 0;
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

.visible_dsk {
    background-color: red;
}

.marketing_stage {
    display: flex;
    flex-grow: 1;
    align-items: center;
}

.contact_add > * > * {
    padding-bottom: 5px;
}

.contact_add_label {
    width: 100px;
}

.close_modal {
    display: flex;
    justify-content: flex-end;
    font-size: 18px;
    align-items: center;
}

>>> .button {
    border: 1px solid gray !important;
}

>>> input {
    border: 1px solid gray;
}

.input_space {
    padding-bottom: 5px;
}

.client_card_header {
    flex-grow: 1;
    padding-left: 10px;
}

.show_all_text {
    font-size: small;
    color: #9b9bff;
    cursor: pointer;
}

.fa-window-close {
    cursor: pointer;
}

.note_text {
    word-break: break-word;
}

.info_mass {
    display: flex;
    flex-direction: column;
    overflow: hidden;
    flex-grow: 1;
}

.spoiler_text {
    font-style: italic;
    font-size: 14px;
    color: black;
}

.notes_button_top{
    width: 50%;
}

.button_margin {
    margin-left: unset;
}

.active_client {
    opacity: 1;
    cursor: pointer;
    background-color: rgb(210, 210, 210);
}

.demand_header{
    color: gray;
    border-bottom: 1px solid;
    font-size: 12px;
}

.demand_options_f {
    display: flex;
    flex-direction: row;
}

.demand_options_f>*:first-child{
    width: 70%;
    display: flex;
    flex-direction: column;
}

.demand_options_f>div>.label{
    flex-grow: unset !important;
}

.demand_options_f>*:first-child>div{
    display: flex;
    flex-direction: column;
    height: 100%;
    flex-grow: 1;
}

>>>.demand_options_f>*:first-child>div>textarea{
    flex-grow: 1;
}

.stage_info{
    display: flex;
    flex-direction: column;
}

.stage_info>div{
    padding-left: 5px;
    padding-bottom: 5px;
}

.stage_info>span{
    padding-left: 5px;
    margin-bottom: 2px;
}
</style>
