<template>
    <div>
        <div class="notification_area">
            <b-button @click="open_chat" type="is-warning" label="Чат">
            </b-button>
            <span v-if="unread > 0" class="dot">{{ unread }}</span>
        </div>
        <b-modal v-model="visible_chat" :width="1400" class="modal_height">
            <div class="giga_chad">
                <div class="chat_groups">
                    <div class="left_block_chat">
                        <b-field class="field_par">
                            <b-button
                                size="is-small"
                                @click="create_chat"
                                label="Новый чат"
                            ></b-button>
                            <b-switch
                                v-model="new_chat.all"
                                :disabled="new_chat.depart"
                                @change.native="choose_all_contacts"
                                v-if="new_chat.isactive"
                                size="is-small"
                                type="is-warning"
                            >
                                Всем сотрудникам
                            </b-switch>
                            <b-switch
                                v-model="new_chat.depart"
                                :disabled="new_chat.all"
                                v-if="new_chat.isactive"
                                @change.native="chose_contact_depart"
                                size="is-small"
                                type="is-warning"
                            >
                                Всем из отдела сотрудника
                            </b-switch>
                        </b-field>
                        <div v-if="!new_chat.isactive" class="dialog_list">
                            <div class="dialog_list_wrapper">
                                <div
                                    class="dialog"
                                    v-for="(dialog, index) in dialogs"
                                    :key="index"
                                    :class="{
                                        active_dialog:
                                            dialog.БеседаКод ==
                                            cur_conversation_id,
                                    }"
                                >
                                    <div
                                        class="dialog_info_area"
                                        @contextmenu.prevent="
                                            open_actions_menu(
                                                $event,
                                                dialog,
                                                'dialog'
                                            )
                                        "
                                    >
                                        <div
                                            class="dialog_header"
                                            @click="
                                                load_cur_dialog_messages(dialog)
                                            "
                                        >
                                            <span class="bold">{{
                                                dialog.БеседаПсевдоним + " :"
                                            }}</span>
                                            <span
                                                v-if="dialog.Участников <= 2"
                                                class=""
                                                >{{
                                                    dialog.Список_Участников
                                                }}</span
                                            >
                                            <span v-else class=""
                                                >Участников:
                                                {{ dialog.Участников }}</span
                                            >
                                        </div>
                                        <div class="dialog_info">
                                            <span class="">{{
                                                dialog.ПоследняяАктивность
                                            }}</span>
                                        </div>
                                    </div>
                                    <div
                                        v-if="
                                            dialog.Непрочитано != null &&
                                            dialog.Непрочитано > 0
                                        "
                                        class="dialog_unread_count"
                                    >
                                        {{ dialog.Непрочитано }}
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div v-if="new_chat.isactive" class="dialog_list">
                            <div class="dialog_list_wrapper">
                                <div
                                    class="dialog"
                                    v-for="contact in new_chat.contacts"
                                    :key="contact.Код"
                                >
                                    <div class="dialog_header">
                                        <span>{{ contact.Сотрудники }}</span>
                                    </div>
                                    <b-checkbox
                                        type="is-warning"
                                        @change.native="add_to_chat(contact)"
                                        v-model="contact.selected"
                                    ></b-checkbox>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="app_flex" v-if="!new_chat.isactive">
                    <div class="window">
                        <span>{{ dialog.БеседаПсевдоним }}</span>
                        <b-button
                            class="btn_show_attachments"
                            size="is-small"
                            type="is-link is-light"
                            @click="show_with_attach_value = true"
                            >Показать вложения</b-button
                        >
                    </div>

                    <div class="list_wrapper" ref="list_wrapper">
                        <div id="list" class="list">
                            <div
                                class="day"
                                v-for="(day, index) in message_list"
                                :day="day"
                                :key="index"
                            >
                                <transition-group name="fade">
                                    <div class="date_title" key="index">
                                        {{ day.date }}
                                    </div>
                                    <div
                                        v-for="(message, index) in day.messages"
                                        :check="
                                            message.readed ? 'true' : 'false'
                                        "
                                        :key="index"
                                        :id="message.id"
                                        :ref="message.ref"
                                    >
                                        <div
                                            class="message_box"
                                            :class="{
                                                right:
                                                    message.user_id ==
                                                    cur_user_id,
                                                left:
                                                    message.user_id !=
                                                        cur_user_id &&
                                                    message.user_id != 561,
                                                info: message.user_id == 561,
                                            }"
                                        >
                                            <div
                                                class="message_avatar"
                                                v-show="
                                                    message.user_id !=
                                                    cur_user_id
                                                "
                                            >
                                                <div
                                                    class="fas fa-user message_user"
                                                ></div>
                                            </div>
                                            <div
                                                class="message_body"
                                                @contextmenu.prevent="
                                                    open_actions_menu(
                                                        $event,
                                                        message,
                                                        'message'
                                                    )
                                                "
                                            >
                                                <div class="message_window">
                                                    <div
                                                        v-if="
                                                            message.user_id !=
                                                            cur_user_id
                                                        "
                                                        class="message_title"
                                                    >
                                                        {{ message.user_name }}
                                                    </div>
                                                    <div
                                                        v-else
                                                        class="message_title"
                                                    >
                                                        Я
                                                    </div>
                                                    <div class="message_dan">
                                                        <button
                                                            v-if="
                                                                deletable(
                                                                    message
                                                                )
                                                            "
                                                            @click="
                                                                deleteItself(
                                                                    message
                                                                )
                                                            "
                                                            class="far fa-trash-alt message_delete"
                                                            title="Удалить сообщение"
                                                        ></button>
                                                        <button
                                                            @click="
                                                                attach(message)
                                                            "
                                                            class="fas fa-paperclip message_attach"
                                                            title="Прикрепить файл"
                                                        ></button>
                                                    </div>
                                                </div>
                                                <div
                                                    class="message_text"
                                                    v-html="wu(message.text)"
                                                ></div>
                                                <div class="message_time">
                                                    {{ message.time }}
                                                </div>
                                                <hr v-if="!!message.files2" />
                                                <div
                                                    class="attach"
                                                    v-for="(
                                                        file, index
                                                    ) in message.files2"
                                                    :key="index"
                                                >
                                                    <button
                                                        id="btn_open_attach"
                                                        class="attach_link"
                                                        @click="
                                                            open_attach(file)
                                                        "
                                                        title="Открыть вложение"
                                                        :link="file.ФайлСсылка"
                                                    >
                                                        {{
                                                            file.ФайлИмя +
                                                            " (" +
                                                            file.ПользовательИмя +
                                                            ", " +
                                                            file.ФайлДата +
                                                            " " +
                                                            file.ФайлВремя +
                                                            ")"
                                                        }}
                                                    </button>
                                                    <i
                                                        class="far fa-trash-alt message_delete"
                                                        title="Удалить вложение"
                                                        @click="
                                                            deattach(file)
                                                        "
                                                    ></i>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </transition-group>
                            </div>
                        </div>

                        <div tabindex="0"></div>
                    </div>

                    <div class="controls2">
                        <textarea
                            id="new_message_field"
                            @keyup.enter="postNewMessage"
                            row="3"
                            class="new_message"
                            v-model="new_message"
                        ></textarea>
                        <button
                            class="new_message_button"
                            @click="postNewMessage"
                        >
                            Отправить
                        </button>
                    </div>

                    <div v-show="show_with_attach_value" class="aa1">
                        <div class="aa2">
                            <div
                                v-for="(day, index) in message_list"
                                :day="day"
                                :key="index"
                            >
                                <div
                                    v-for="message in day.messages"
                                    :message="message"
                                    :cur_user_id="cur_user_id"
                                    :key="message.id"
                                >
                                    <div
                                        class="aa3"
                                        v-for="(file, index) in message.files2"
                                        :key="index"
                                    >
                                        <button
                                            id="btn_open_attach"
                                            class="aa4"
                                            @click="open_attach(file)"
                                            title="Открыть вложение"
                                            :link="file.ФайлСсылка"
                                        >
                                            {{
                                                file.ФайлИмя +
                                                " (" +
                                                file.ПользовательИмя +
                                                ", " +
                                                file.ФайлДата +
                                                " " +
                                                file.ФайлВремя +
                                                ")"
                                            }}
                                        </button>
                                    </div>
                                </div>
                            </div>
                            <button
                                @click="show_with_attach_value = false"
                                class="btn_hide_attachments"
                            >
                                Скрыть вложения
                            </button>
                        </div>
                    </div>

                    <div v-show="modal.rename.visible" class="aa1">
                        <div class="rename aa2">
                            <input
                                class="my_input"
                                v-model="modal.rename.new_name"
                                placeholder="Введите название чата"
                            />
                            <button
                                class="my_button"
                                @click="
                                    chat_action_send(
                                        modal.rename.id,
                                        modal.rename.cmd,
                                        modal.rename
                                    )
                                "
                            >
                                Сохранить
                            </button>
                            <button class="my_button" @click="hide_rename">
                                Скрыть
                            </button>
                        </div>
                    </div>

                    <div v-show="modal.add_user.visible" class="aa1">
                        <div class="add_user aa2">
                            <div class="contact_wrapper">
                                <div
                                    v-for="contact in new_chat.contacts"
                                    :key="contact.Код"
                                >
                                    <div>{{ contact.Сотрудник }}</div>
                                    <b-checkbox
                                        type="is-warning"
                                        @change.native="add_to_chat(contact)"
                                        v-model="contact.selected"
                                    ></b-checkbox>
                                </div>
                            </div>
                            <button
                                class="my_button"
                                @click="
                                    chat_action_send(
                                        modal.add_user.id,
                                        modal.add_user.cmd,
                                        modal.add_user
                                    )
                                "
                            >
                                Сохранить
                            </button>
                            <button
                                class="my_button"
                                @click="modal.add_user.visible = false"
                            >
                                Отмена
                            </button>
                        </div>
                    </div>

                    <div v-show="modal.delete_user.visible" class="aa1">
                        <div class="add_user aa2">
                            <div class="contact_wrapper">
                                <div
                                    v-for="(contact, index) in modal.delete_user
                                        .users"
                                    :key="index"
                                >
                                    <div>{{ contact.Сотрудник }}</div>
                                    <b-checkbox
                                        type="is-warning"
                                        @change.native="add_to_chat(contact)"
                                        v-model="contact.selected"
                                    ></b-checkbox>
                                </div>
                            </div>
                            <button
                                class="my_button"
                                @click="
                                    chat_action_send(
                                        modal.delete_user.id,
                                        modal.delete_user.cmd,
                                        new_chat.added
                                    )
                                "
                            >
                                Удалить выбранных
                            </button>
                            <button
                                class="my_button"
                                @click="set_actions_default"
                            >
                                Отмена
                            </button>
                        </div>
                    </div>
                </div>
                <div v-else class="app_flex">
                    <div class="new_chat_info">
                        <div>Выберите участников чата</div>
                        <input
                            v-model="new_chat.name"
                            v-if="new_chat.added.length > 1"
                            class="input"
                            placeholder="Введите название чата"
                        />
                        <div class="new_chat_contacts">
                            <div
                                v-for="added in new_chat.added"
                                :key="added.Код"
                                class="added_card added_to"
                            >
                                <div class="added_name">
                                    <i class="fas fa-user"></i>
                                    <span>{{ added.Сотрудник }}</span>
                                </div>
                            </div>
                            <div
                                v-for="added in new_chat.default_added"
                                :key="added.Код"
                                class="added_card"
                            >
                                <div class="added_name">
                                    <i class="fas fa-user"></i>
                                    <span>{{ added.Сотрудник }}</span>
                                </div>
                            </div>
                        </div>
                        <div class="">
                            <button
                                class="button"
                                @click="new_chat.isactive = false"
                            >
                                Отмена
                            </button>
                            <button class="button" @click="create_dialog">
                                Создать чат
                            </button>
                        </div>
                    </div>
                </div>
            </div>
            <b-loading
                :is-full-page="isFullPage"
                v-model="isLoading"
            ></b-loading>
            <transition name="fade">
                <div v-click-outside="hide_context_menu">
                    <ul
                        v-if="context_menu.visible.message"
                        :style="{
                            top: context_menu.top,
                            left: context_menu.left,
                        }"
                        class="context_menu mat_des_shadow"
                        ref="contextMenu"
                    >
                        <li
                            class="cont_menu"
                            v-for="(action, index) in context_menu.actions"
                            :key="index"
                            @click="action_click(action)"
                        >
                            {{ action }}
                        </li>
                    </ul>
                </div>
            </transition>
            <transition name="fade">
                <div v-click-outside.once="hide_context_menu">
                    <ul
                        v-if="context_menu.visible.dialog"
                        :style="{
                            top: context_menu.top,
                            left: context_menu.left,
                        }"
                        class="context_menu mat_des_shadow"
                        ref="contextMenu"
                    >
                        <li
                            class="cont_menu"
                            @click="chat_action('rename')"
                            aria-role="listitem"
                        >
                            Переименовать
                        </li>
                        <li
                            class="cont_menu"
                            @click="chat_action('add_user')"
                            aria-role="listitem"
                        >
                            Добавить собеседника
                        </li>
                        <li
                            class="cont_menu"
                            v-if="context_menu.m.Инициатор == cur_user_id"
                            @click="chat_action('remove_user')"
                            aria-role="listitem"
                        >
                            Удалить собеседника
                        </li>
                        <li
                            class="cont_menu"
                            @click="chat_action('unsub')"
                            aria-role="listitem"
                        >
                            Отписаться
                        </li>
                        <li
                            class="cont_menu"
                            v-if="context_menu.m.Инициатор == cur_user_id"
                            @click="chat_action('remove')"
                            aria-role="listitem"
                        >
                            Удалить беседу
                        </li>
                    </ul>
                </div>
            </transition>
        </b-modal>
    </div>
</template>

<script>
import axios from 'axios'
import * as my_f from "@/my"

export default  {

    directives: {
        message: {
            bind() {
                // console.log(el)
            },
        },
    },

    components: {},
    props: ["unread"],
    data() {
        return {
            check: "",
            cur_conversation_id: "",
            cur_user_id: this.$store.getters.USER_ID,
            message_list: [
                {
                    date: "",
                    messages: "",
                },
            ],
            messages_flat: [],
            context_menu: {
                visible: {
                    dialog: false,
                    message: false,
                },
                acitions: [],
                top: "0px",
                left: "0px",
                m: {},
            },
            new_chat: {
                isactive: false,
                contacts: [],
                added: [],
                default_added: [{ Сотрудник: null }],
                depart: false,
                all: false,
                name: null,
            },
            modal: {
                rename: {
                    visible: false,
                    new_name: null,
                    cmd: null,
                    id: null,
                },

                add_user: {
                    visible: false,
                    cmd: null,
                    id: null,
                    users: [],
                },

                delete_user: {
                    visible: false,
                    cmd: null,
                    id: null,
                    users: [],
                },
            },
            dialogs: [],
            dialog: {},
            new_message: "",
            visible_chat: false,
            isLoading: false,
            isFullPage: false,

            filter_text: "",
            refresh: false,
            first_load: true,

            show_with_attach_value: false,
            url: this.$store.getters.api_url_new,
        };
    },

    computed: {
        check_update() {
            return this.$store.getters.messages.unread;
        },
    },

    watch: {
        check_update(new_, old) {
            console.log(new_, old);
            if (new_ > old) {
                this.load_dialog_list();
                if (Object.keys(this.dialog).length > 0 && this.visible_chat) {
                    this.load_cur_dialog_messages(this.dialog);
                }
            }
        },
    },

    updated: function () {
        if (!this.visible_chat) return;
        if (!this.first_load) return;
        if (Object.keys(this.dialog).length == 0) return;
        this.first_load = false;
        // пролистываем чат до непрочитанного сообщения
        if (
            typeof this.$refs.not_readed_first === "undefined" ||
            this.$refs.not_readed_first.length == 0
        ) {
            if (typeof this.$refs.readed === "undefined") return;
            var i = this.$refs.readed.length - 1;
            console.log("tut");
            var end = this.$refs.readed[i];
            end.scrollIntoView({
                block: "end",
                inline: "nearest",
                behavior: "smooth",
            });
        } else {
            end = this.$refs.not_readed_first[0];
            console.log("ili tut");
            end.scrollIntoView({
                block: "end",
                inline: "nearest",
                behavior: "smooth",
            });
            const observer = new IntersectionObserver(this.scrollTracking, {
                threshold: 0.2,
            });
            this.$refs.not_readed_first.forEach((el) => observer.observe(el));
        }
    },

    mounted() {
        this.load_dialog_list();
        console.log(this.$app)
    },

    methods: {
        /*

            нужны функции:
            - добавить сообщение
            - удалить сообщение
            - прикрепить файл
            - удалить файл


        */

        show_with_attach: function () {
            this.show_with_attach_value = !this.show_with_attach_value;
        },

        deleteMessage: function (id) {
            //удаляем сообщение
            alert(id);
            return;

            // axios
            // .get(this.url, {params: {cmd: 'deleteMessage', message_id: m.id, user_id: app.cur_user_id, t: new Date()}})
            // .then(function(response){
            //     app.first_load = true
            //     app.loadData()
            // })
        },

        postNewMessage: function () {
            //отправляем на сервер новое сообщение

            this.isLoading = true;
            axios
                .get(this.url, {
                    params: {
                        cmd: "postNewMessage",
                        conversation_id: this.cur_conversation_id,
                        user_id: this.cur_user_id,
                        text: this.new_message,
                        t: new Date(),
                    },
                })
                .then(() => {
                    this.first_load = true;
                    this.loadData();
                    this.isLoading = false;
                });
            this.new_message = "";
        },

        toEnd: function () {
            // scrollToElement("end");
        },

        scrollTracking(entries) {
            for (const entry of entries) {
                console.log(entry);
                if (
                    entry.target.attributes.check.value == "false" &&
                    entry.intersectionRatio >= 0.2
                ) {
                    this.set_message_readed(entry.target.id);
                }
            }
        },

        set_message_readed(id) {
            this.set_message_readed_by_id(id);
            var bodyFormData = new FormData();
            bodyFormData.append("cmd", "set_message_readed");
            bodyFormData.append("chat", this.cur_conversation_id);
            bodyFormData.append("id", id);
            bodyFormData.append("user", this.cur_user_id);
            axios({
                method: "post",
                url: this.url,
                data: bodyFormData,
                headers: { "Content-Type": "multipart/form-data" },
            }).then((response) => {
                console.log(response.data);
                if (response.data.result == "-ok") {
                    if (this.cur_conversation_id == this.dialog.БеседаКод) {
                        this.set_refs(this.message_list);
                        this.$set(
                            this.dialog,
                            "Непрочитано",
                            this.dialog.Непрочитано - 1
                        );
                    }
                }
            });
        },

        set_message_readed_by_id(id) {
            Object.values(this.message_list).forEach((el) => {
                Object.values(el.messages).find((elem) => {
                    elem.id == id ? (elem.readed = true) : null;
                });
            });
        },

        loadData: function () {
            // загружаем заказы с сервера
            if (this.cur_conversation_id == "") return;
            axios
                .get(this.url, {
                    params: {
                        cmd: "getMessageList",
                        conversation_id: this.cur_conversation_id,
                        user: this.cur_user_id,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    this.first_load = true;
                    this.message_list = response.data;
                    this.set_refs(this.message_list);
                });
        },

        load_cur_dialog_messages: function (dialog) {
            this.isLoading = true;
            this.dialog = dialog;
            axios
                .get(this.url, {
                    params: {
                        cmd: "getMessageList",
                        conversation_id: dialog.БеседаКод,
                        user: this.cur_user_id,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    this.message_list = response.data;
                    this.set_refs(this.message_list);
                    this.first_load = true;
                    this.isLoading = false;
                    this.cur_conversation_id = dialog.БеседаКод;
                });
        },

        set_refs(obj) {
            Object.values(obj).forEach((el) => {
                Object.values(el.messages).forEach((elem) => {
                    if (!elem.readed)
                        this.$set(elem, "ref", "not_readed_first");
                    else this.$set(elem, "ref", "readed");
                });
            });
        },

        load_dialog_list: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "get_dialogs_list",
                        user_id: this.cur_user_id,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    this.dialogs = response.data.dialogs;
                    response.data.contacts.forEach((el) => {
                        this.$set(el, "selected", false);
                    });
                    this.new_chat.contacts = response.data.contacts;
                    // console.log(response.data)
                });
        },

        load_unread_messages: function () {
            axios
                .get(this.url, {
                    params: {
                        cmd: "get_unread_messages",
                        user_id: this.cur_user_id,
                        t: new Date(),
                    },
                })
                .then((response) => {
                    if (response.data.UNREAD > "100") {
                        this.unread = "99+";
                    } else {
                        this.unread = response.data;
                    }
                    // console.log(response.data)
                });
        },

        attach: function (m) {
            //прикрепляем файл
            my_f.selectFile(function (files) {
                // загружаем файлы по одному
                for (var i = 0; i < files.length; i++) {
                    my_f.uploadFile(
                        {
                            cmd: "attachMessageAttachment",
                            id: m.id,
                            userfile: files[i],
                            user: this.cur_user_id,
                        },
                        this.loadData,
                        this.url
                    );
                }
            });
        },

        deattach: function (f) {
            // открепляем вложение
            axios
                .get(this.url, {
                    params: {
                        cmd: "deattach",
                        file_id: f.ФайлКод,
                        user_id: this.cur_user_id,
                        t: new Date(),
                    },
                })
                .then(() => {
                    this.loadData();
                });
        },

        deleteItself: function (m) {
            // удалить сообщение
            // нужен контроль прав удаления

            if (!confirm("Вы действительно хотите удалить сообщение?")) return;

            axios
                .get(this.url, {
                    params: {
                        cmd: "deleteMessage",
                        message_id: m.id,
                        user_id: this.cur_user_id,
                        t: new Date(),
                    },
                })
                .then(() => {
                    this.loadData();
                });
        },

        deletable: function (m) {
            // удалять может только автор
            if (m.user_id != this.cur_user_id) return false;
            // удалять можно сообщения, у которых нет просмотров
            // удалять можно сообщения, у которых нет дочерних элементов (задач и вложений)
            // лучше проверки этих условий проводить на сервере
            return true;
        },

        wu: function (str) {
            return my_f.wrapURL(str);
        },

        open_chat: function () {
            this.visible_chat = true;
        },

        open_actions_menu2: function () {
            this.context_menu.visible = true;
        },

        setMenu: function (top, left) {
            // let largestHeight
            // let largestWidth
            // largestHeight =
            //     this.$refs.list_wrapper.offsetHeight -
            //     this.$refs.contextMenu.offsetHeight -
            //     10;
            // largestWidth =
            //     this.$refs.list_wrapper.offsetWidth -
            //     this.$refs.contextMenu.offsetWidth -
            //     10;
            this.context_menu.top = top + "px";
            this.context_menu.left = left + "px";
        },

        closeMenu: function () {
            this.context_menu.visible = false;
        },

        open_actions_menu: function (e, message, par) {
            if (par == "dialog") {
                this.context_menu.visible.dialog = true;
            } else {
                this.context_menu.visible.message = true;
            }
            if (this.deletable(message)) {
                this.context_menu.actions = [
                    "Ответить",
                    "Переслать",
                    "Удалить",
                    "Копировать",
                ];
            } else {
                this.context_menu.actions = [
                    "Ответить",
                    "Переслать",
                    "Копировать",
                ];
            }
            this.context_menu.m = message;
            this.$nextTick(
                function () {
                    this.$refs.contextMenu.focus();
                    this.setMenu(e.y, e.x);
                }.bind(this)
            );
            e.preventDefault();
        },

        action_click(action) {
            this.context_menu.visible.message = false;
            switch (action) {
                case "Ответить":
                    this.reply_to(this.context_menu.m);
                    break;
                case "Переслать":
                    this.resend_to(this.context_menu.m);
                    break;
                case "Удалить":
                    this.deleteItself(this.context_menu.m);
                    break;
                case "Копировать":
                    this.copy_text(this.context_menu.m);
                    break;
            }
        },

        reply_to() {
            this.$alert("Функция в разработке", "is-warning");
        },

        resend_to() {
            this.$alert("Функция в разработке", "is-warning");
        },

        copy_text() {
            this.$my_f.clipboard_set_text(this.context_menu.m.text);
            this.$alert("Скопировано в буфер");
        },

        create_chat() {
            this.new_chat.isactive = !this.new_chat.isactive;
        },

        choose_all_contacts() {
            if (this.new_chat.all) {
                this.new_chat.contacts.forEach((el) => {
                    if (
                        this.new_chat.added.indexOf(el) === -1 &&
                        el.Код != this.cur_user_id
                    ) {
                        el.selected = true;
                        this.new_chat.added.push(el);
                    }
                });
            } else {
                this.new_chat.contacts.forEach((el) => {
                    el.selected = false;
                    this.new_chat.added = [];
                });
            }
        },

        chose_contact_depart() {
            if (this.new_chat.added.length == 0) {
                this.new_chat.depart = false;
                return this.$alert(
                    "Выберите сотрудника из отдела",
                    "is-warning"
                );
            }
            if (this.new_chat.depart) {
                this.new_chat.contacts.find((el) => {
                    if (
                        el.отдел_код == this.new_chat.added[0].отдел_код &&
                        this.new_chat.added.indexOf(el) === -1 &&
                        el.Код != this.cur_user_id
                    ) {
                        this.new_chat.added.push(el);
                        el.selected = true;
                    }
                });
            } else {
                this.new_chat.added = [];
                this.new_chat.contacts.find((el) => {
                    el.selected = false;
                });
            }
        },

        add_to_chat(par) {
            if (par.selected) {
                this.new_chat.added.push(par);
            } else {
                this.new_chat.added.splice(this.new_chat.added.indexOf(par), 1);
            }
        },

        create_dialog() {
            this.isLoading = true;
            var bodyFormData = new FormData();
            bodyFormData.append("cmd", "create_dialog");
            bodyFormData.append("users", JSON.stringify(this.new_chat.added));
            bodyFormData.append("user", this.cur_user_id);
            if (
                this.new_chat.added.length > 1 &&
                this.new_chat.name != null &&
                this.new_chat.name != ""
            ) {
                bodyFormData.append("text", this.new_chat.name);
            } else if (this.new_chat.all) {
                bodyFormData.append("text", "Все сотрудники");
            } else if (this.new_chat.depart) {
                bodyFormData.append("text", this.new_chat.added[0].Отдел);
            } else {
                bodyFormData.append("text", this.new_chat.added[0].Сотрудник);
            }
            axios({
                method: "post",
                url: this.url,
                data: bodyFormData,
                headers: { "Content-Type": "multipart/form-data" },
            }).then((response) => {
                this.isLoading = false;
                console.log(response.data);
                if (response.data.result == "-ok") {
                    if (typeof response.data.dialog_id !== "undefined") {
                        this.load_cur_dialog_messages({
                            БеседаКод: response.data.dialog_id,
                        });
                    } else {
                        this.dialogs = response.data.dialogs;
                        this.load_cur_dialog_messages({
                            БеседаКод: response.data.new_dialog_id,
                        });
                        this.$alert("Беседа успешно создана", "is-success");
                    }
                    this.new_chat_set_default();
                } else {
                    this.$alert(
                        "Не удалось создать беседу, попробуйте позже",
                        "is-warning"
                    );
                }
            });
        },

        new_chat_set_default() {
            this.new_chat.isactive = false;
            this.new_chat.all = false;
            this.new_chat.depart = false;
            this.new_chat.added = [];
            this.new_chat.contacts.forEach((el) => {
                el.selected = false;
            });
            this.new_chat.name = null;
        },

        chat_action(par) {
            this.context_menu.visible.dialog = false;
            switch (par) {
                case "rename":
                    return this.rename(this.context_menu.m, par);
                case "add_user":
                    return this.sub_user(this.context_menu.m, par);
                case "remove_user":
                    return this.delete_user(this.context_menu.m, par);
                case "unsub":
                    return this.unsub(this.context_menu.m, par);
                case "remove":
                    return this.delete(this.context_menu.m, par);
            }
        },

        rename(dialog, par) {
            this.modal.rename.visible = true;
            this.modal.rename.cmd = par;
            this.modal.rename.id = dialog.БеседаКод;
            this.modal.rename.new_name = dialog.БеседаПсевдоним;
        },

        hide_rename() {
            this.modal.rename.visible = false;
        },

        sub_user(dialog, par) {
            this.modal.add_user.visible = true;
            this.modal.add_user.cmd = par;
            this.modal.add_user.id = dialog.БеседаКод;
            this.modal.add_user.new_name = dialog.БеседаПсевдоним;
            this.modal.add_user.users = this.new_chat.added;
        },

        delete_user(dialog, par) {
            this.modal.delete_user.visible = true;
            this.modal.delete_user.cmd = par;
            this.modal.delete_user.id = dialog.БеседаКод;
            let list = dialog.Список_Участников.split(";");
            for (let i = 0; i < list.length; i++) {
                if (list[i] != "" && list[i] != "Я") {
                    this.modal.delete_user.users.push({
                        Сотрудник: list[i],
                        selected: false,
                    });
                }
            }
        },

        unsub(dialog, par) {
            this.chat_action_send(dialog.БеседаКод, par, this.cur_user_id);
        },

        delete(dialog, par) {
            this.chat_action_send(dialog.БеседаКод, par, this.cur_user_id);
        },

        set_actions_default() {
            this.modal.rename.cmd = null;
            this.modal.rename.id = null;
            this.modal.rename.new_name = null;
            this.modal.rename.visible = false;
            this.modal.add_user.cmd = null;
            this.modal.add_user.id = null;
            this.modal.add_user.visible = false;
            this.modal.delete_user.cmd = null;
            this.modal.delete_user.id = null;
            this.modal.delete_user.visible = false;
            this.modal.delete_user.users = [];
        },

        chat_action_send(id, par, data) {
            this.isLoading = true;
            var bodyFormData = new FormData();
            bodyFormData.append("cmd", "dialog_action");
            bodyFormData.append("chat_id", id);
            bodyFormData.append("par", par);
            bodyFormData.append("user", this.cur_user_id);
            bodyFormData.append("data", JSON.stringify(data));

            axios({
                method: "post",
                url: this.url,
                data: bodyFormData,
                headers: { "Content-Type": "multipart/form-data" },
            }).then((response) => {
                this.set_actions_default();
                this.new_chat_set_default();
                this.isLoading = false;
                console.log(response.data);
                if (response.data.result == "-ok") {
                    this.dialogs = response.data.dialogs;
                    if (par != "unsub") {
                        this.load_cur_dialog_messages({ БеседаКод: id });
                    } else {
                        this.message_list = [
                            {
                                date: "",
                                messages: "",
                            },
                        ];
                    }
                } else {
                    this.$parent.alert(
                        "Не удалось завершить операцию, попробуйте позже"
                    );
                }
            });
        },

        hide_context_menu() {
            this.context_menu.visible.dialog = false;
            this.context_menu.visible.message = false;
        },
    },
};
</script>

<style scoped>
.app_flex {
    display: flex;
    flex-direction: column;
    background: rgb(239, 239, 209);
    width: 100%;
    height: 100%;
    overflow: hidden;
}

.list_wrapper {
    margin: 5px;
    border-radius: 5px;
    border: 1px solid lightgray;
    flex-grow: 1;
    overflow: auto;
    background: white;
    max-height: calc(100% - 100px);
    height: 100%;
}

.list {
    display: flex;
    flex-direction: column;
    flex: 1 0 auto;
    overflow: auto;

    /*
        ВНИМАНИЕ! 
        РЕШЕНИЕ ПРОБЛЕМЫ В IE
        ЧТОБЫ СОДЕРЖИМОЕ ПРАВИЛЬНО РАСТЯГИВАЛОСЬ ПО ВЕРТИКАЛИ:
        1. ДОБАВЛЯЕМ flex: 1 0 auto;
        2. САМ БЛОК С СОДЕРЖИМЫМ ПОМЕЩАЕМ В ДРУГОЙ БЛОК, В КОТОРОМ РЕАЛИЗУЕМ СКРОЛ
    */
}

.day {
    margin-bottom: 5px;
}

.controls2 {
    height: 100px;

    flex-grow: 0;

    display: flex;
    align-content: flex-end;
}

.controls2 > * {
    border: 1px solid lightgray;
    font-size: 12pt;
    padding: 5px;
    margin: 5px;
    border-radius: 5px;
}

.new_message {
    flex-grow: 1;
}

.message_box {
    margin: 5px;
    margin-right: auto;
    margin-left: auto;
    max-width: 70%;
    display: flex;
    flex-direction: row;
}

.message_avatar {
    margin: 5px;
    padding: 0px;
    background: rgb(239, 239, 239);
    width: 60px;
    height: 60px;
    min-width: 60px;
    border-radius: 30%;

    display: flex;
    justify-content: center;
    align-items: center;

    border: 1px solid lightgray;

    display: none;
}

.message_body {
    margin: 5px;
    padding: 10px;
    background: rgb(239, 239, 239);
    border-radius: 0 20px 20px 20px;
    border: 1px solid lightgray;
}

.message_title {
    padding: 0px;
    color: rgb(133, 145, 223);
    font-weight: bold;
    font-size: 12pt;
}

.message_text {
    padding: 0px;
    margin: 0px;
    color: rgb(30, 30, 30);
    font-size: 12pt;
    line-height: 14pt;
}

.message_time {
    display: inline-block;
    font-size: 10pt;
    color: rgb(150, 150, 150);
    width: auto;
    margin-left: auto;
    margin-right: 5px;
}

.right {
    margin-right: 0px;
    justify-content: flex-end;
}

.left {
    margin-left: 0px;
    justify-content: flex-start;
}

.right > .message_body {
    border-radius: 20px 0 20px 20px;
}

.left > .message_body {
    border-radius: 0 20px 20px 20px;
}

.date_title {
    background: none;
    color: gray;
    margin: auto;
    padding: 5px;

    text-align: center;
    font-weight: bold;
}

.empty_avatar {
    margin: 0px;
    color: white;
    font-size: 36pt;
}

.controls {
    position: fixed;
    top: calc(100% - 65px);
    right: 5px;
}

.controls > button {
    border-radius: 50%;
    width: 50px;
    height: 50px;
    border: 1px solid lightgray;
    margin: 5px;
    padding: 2px;
    text-align: center;
    outline: none;
}

.controls > button:hover {
    background: orange;
}

hr {
    padding: 0px;
    margin-bottom: 0px;
}

.attach > button,
.attach_new {
    border: none;
    outline: none;
    background: none;
    font-size: 8pt;
    cursor: pointer;
    padding: 0px;
    margin: 0px;
}

.attach_link:hover,
.attach_new:hover {
    text-decoration: underline;
    color: blue;
}

.attach_del {
    color: grey;
}

.attach_del {
    text-decoration: underline;
    color: red;
}

.attach:hover .attach_del {
    display: inline-block;
}

.aa1 {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    flex-direction: column;
}

.aa2 {
    padding: 5px;
    background: lightgray;
    width: 75%;
    max-width: 300px;
    margin: auto;
    border-radius: 5px;
}

.aa4 {
    margin: 2px auto;
    width: 100%;
    border: 1px solid gray;
    border-radius: 5px;
}

.aa4:hover {
    color: blue;
    text-decoration: underline;
}
.window {
    padding: 5px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.window > span {
    font-weight: bold;
}

.btn_hide_attachments {
    width: 100%;
    margin: 5px 0px;
}
.message_user {
    margin: 0px;
    color: white;
    font-size: 36pt;
}
.message_window {
    display: flex;
}
.message_dan {
    margin-right: 0px;
    margin-left: auto;
    padding-left: 15px;
}
.message_delete {
    background: none;
    border: none;
    font-size: 11pt;
    color: gray;
    margin: 0px;
    padding: 0px;
}
.message_attach {
    background: none;
    border: none;
    font-size: 11pt;
    color: gray;
    margin: 0px;
}

>>>.modal-content {
    overflow: hidden;
    height: 100%;
}

>>>.modal-background {
    background-color: rgba(54, 54, 54, 0.86);
}

.giga_chad {
    display: flex;
    flex-direction: row;
    height: 100%;
    overflow: hidden;
}

.chat_groups {
    width: 36%;
    height: 100%;
    background: rgb(239, 239, 209);
    border-right: 1px solid lightgray;
}

.dialog_list {
    padding: 5px;
    overflow-y: auto !important;
    height: calc(100% - 60px);
}

.left_block_chat {
    height: 100%;
}

.dialog {
    padding: 5px;
    border: 1px solid lightgray;
    border-radius: 18px;
    margin: 5px;
    background-color: white;
    box-shadow: rgb(0 0 0 / 1%) 0px 3px 6px, rgb(0 0 0 / 16%) 0px 3px 6px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.dialog_header:hover {
    cursor: pointer;
}

.dialog:hover {
    cursor: pointer;
    background-color: whitesmoke;
}

.active_dialog {
    background-color: rgb(235, 233, 233);
}

.dialog_info_area {
    width: 90%;
}

.dialog_unread_count {
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(255, 94, 0, 0.68);
    box-shadow: rgb(255 94 0 / 68%) 0px 0px 1px 0px;
    padding: 1px 5px;
    color: white;
    display: flex;
    width: 27px;
    justify-content: center;
}

.dialog_info {
    border-top: 1px solid gray;
    display: flex;
    justify-content: space-between;
}

>>>.field_par {
    margin-bottom: unset !important;
    padding: 5px;
    border-bottom: 1px solid lightgray;
    display: flex;
}

::-webkit-scrollbar {
    width: 7px;
    opacity: 0;
}

::-webkit-scrollbar-thumb {
    border-width: 1px 1px 1px 2px;
    border-radius: 25px;
    border-color: rgb(128, 128, 128);
    background-color: rgb(167, 167, 167);
}

::-webkit-scrollbar-thumb:hover {
    border-width: 1px 1px 1px 2px;
    border-color: rgb(109, 108, 108);
    background-color: rgb(126, 126, 126);
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

.dot {
    position: absolute;
    top: 0px;
    right: 0px;
    padding: 1px 5px;
    border-radius: 50%;
    background: red;
    color: white;
    display: flex;
    width: 27px;
    justify-content: center;
    box-shadow: red 0px 0px 1px 0px;
    animation: pulsate 2s ease-out;
    -webkit-animation: pulsate 2s ease-out;
    animation-iteration-count: infinite;
    -webkit-animation-iteration-count: infinite;
    opacity: 0;
}
.context_menu {
    position: fixed;
    background-color: #fff;
    border-radius: 4px;
    box-shadow: 0 0.5em 1em -0.125em rgb(10 10 10 / 10%),
        0 0 0 1px rgb(10 10 10 / 2%);
    padding-bottom: 0.5rem;
    padding-top: 0.5rem;
}

.cont_menu {
    padding-right: 3rem;
    text-align: inherit;
    white-space: nowrap;
    width: 100%;
    color: #4a4a4a;
    display: block;
    font-size: 0.875rem;
    line-height: 1.5;
    padding: 0.375rem 1rem;
    cursor: pointer;
}

.cont_menu:hover {
    background-color: #f5f5f5;
    color: #0a0a0a;
}

.notification_area {
    position: relative;
    width: 113px;
}

@keyframes pulsate {
    0% {
        opacity: 0.5;
    }
    50% {
        opacity: 1;
    }
    100% {
        opacity: 0.5;
    }
}

.active_dropdown {
    overflow: unset;
}

.b-checkbox.checkbox input[type="checkbox"] + .check {
    border-radius: 50%;
}

.new_chat_info {
    width: 76%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    margin: 0 auto;
}

.new_chat_info > button {
    height: 30px;
}

.new_chat_info > input {
    width: 30%;
    margin-bottom: 5px;
}

.added_name {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    height: 100%;
}

.added_name > i {
    font-size: 50px;
}

.added_to {
    background: lightgray;
}

.new_chat_contacts {
    display: flex;
    flex-flow: row wrap;
    /* width: 76%; */
    overflow: auto;
    max-height: 35%;
    align-items: flex-end;
    align-content: flex-start;
}

.new_chat_contacts::after {
    content: "";
    flex: auto;
}

.added_card {
    height: 100px;
    width: 180px;
    border: 2px dashed gray;
    border-radius: 35%;
    margin: 5px;
    padding: 5px;
}

.rename {
    display: flex;
    flex-direction: column;
}

.rename > * {
    margin: 5px;
    height: 30px;
}

.add_user {
    display: flex;
    flex-direction: column;
    height: 500px;
    overflow: hidden;
}

.contact_wrapper {
    overflow: auto;
    height: 430px;
}

.contact_wrapper > * {
    display: flex;
    justify-content: space-between;
}

.my_input {
    color: #363636;
    border: 1px solid #bfbfbf;
    border-radius: 4px;
    box-shadow: none;
    display: inline-flex;
    font-size: 14px;
    padding-bottom: calc(0.5em - 1px);
    padding-left: calc(0.75em - 1px);
    padding-right: calc(0.75em - 1px);
    padding-top: calc(0.5em - 1px);
    outline: none;
    widows: 50%;
}

.my_button {
    border-radius: 2px;
    background-color: rgb(255, 255, 255);
    color: rgb(54, 54, 54);
    cursor: pointer;
    justify-content: center;
    padding: calc(0.5em - 1px) 1em;
    text-align: center;
    white-space: nowrap;
    border: 1px solid lightgray;
}

.info {
    justify-content: center;
}

.info > .message_body {
    border-radius: 20px 20px 20px;
    background: antiquewhite;
}

.info > .message_body > .message_window {
    display: none;
}

.info > .message_body > .message_time {
    display: none;
}

.dialog_context_menu {
}
</style>
