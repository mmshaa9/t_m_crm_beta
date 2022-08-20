<template>
    <div>
        <transition name="zoom-out">
            <div v-if="select.isactive">
                <div
                    class="modal_background"
                    ref="draggableContainer"
                    id="draggable-container"
                >
                    <div
                        class="modal_card_box mat_des_shadow"
                        :style="{width: select.width + 'px'}"
                    >
                        <div
                            class="header_modal_box"
                            :class="{grabble: positions.active}"
                            id="draggable-header"
                            @mousedown.capture="dragMouseDown"
                        >
                            <span>{{ select.header_modal }}</span>
                            <i
                                @click="close"
                                class="fas fa-times"
                            ></i>
                        </div>
                        <div class="modal_area">
                            <input
                                v-model="select.filter_text"
                                v-if="select.filter"
                                @input="$emit('select_box_filter')"
                                placeholder="Поиск"
                                class="select_box_input"
                                type="search"
                            />
                            <div
                                class="item_modal_list"
                                :style="{
                                    'min-height':
                                        select.min_height != undefined
                                            ? select.min_height + 'px'
                                            : 150 + 'px',
                                }"
                            >
                                <div
                                    v-if="
                                        typeof select.multi == 'undefined' ||
                                        !select.multi
                                    "
                                    @click="cur_item = item"
                                    @dblclick="select.set_action(item)"
                                    class="item_modal_row"
                                    v-for="(item, index) in filtered_items"
                                    :key="index"
                                    :class="{
                                        choosen_par:
                                            cur_item.name == item.name,
                                    }"
                                >
                                    {{ item.name }}
                                </div>
                                <div
                                    v-if="select.multi"
                                    @click="select.select_option(item)"
                                    class="item_modal_row"
                                    v-for="(item, index) in filtered_items"
                                    :key="index"
                                    :class="{
                                        choosen_par:
                                            select.active_item.indexOf(item) !=
                                            -1,
                                    }"
                                >
                                    {{ item.name }}
                                </div>
                            </div>
                            <div class="bottom_buttons">
                                <b-button
                                    size="is-small"
                                    @click="
                                        select.set_action(cur_item)
                                    "
                                >
                                    Ок
                                </b-button>
                                <b-button
                                    size="is-small"
                                    @click="close"
                                    class="cancel_button"
                                >
                                    Отмена
                                </b-button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
import {mapActions, mapState} from "vuex";
export default {
    data: function () {
        return {
            cur_item: [],
            positions: {
                clientX: undefined,
                clientY: undefined,
                movementX: -0,
                movementY: -0,
                active: false,
            },
        };
    },

    computed: {
        ...mapState(["select"]),

        default() {
            return this.select.isactive;
        },

        filtered_items() {
            if (this.select.filter_text != null) {
                if (this.select.filter_text.length > 0) {
                    return this.select.items.filter((e) => {
                        return e.name
                            .toLowerCase()
                            .includes(this.select.filter_text.toLowerCase());
                    });
                }
            }
            return this.select.items;
        },
    },

    watch: {
        default() {
            if (!this.select.isactive) {
                this.cur_item = [];
                this.select.filter_text = "";
                this.select.active_item = [];
            }
        },
    },

    created(){
        if(this.select.isactive){
            this.close()
        }
    },

    methods: {

        ...mapActions(["close_select"]),

        close(){
            this.close_select()
        },
        dragMouseDown: function (event) {
            event.preventDefault();
            // get the mouse cursor position at startup:
            this.positions.clientX = event.clientX;
            this.positions.clientY = event.clientY;
            document.onmousemove = this.elementDrag;
            document.onmouseup = this.closeDragElement;
        },
        elementDrag: function (event) {
            this.positions.active = true;
            event.preventDefault();
            this.positions.movementX = this.positions.clientX - event.clientX;
            this.positions.movementY = this.positions.clientY - event.clientY;
            this.positions.clientX = event.clientX;
            this.positions.clientY = event.clientY;
            // set the element's new position:
            // this.$refs.draggableContainer.style.transform = 'translate3d(' + ((this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px,' + (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px, 0)')
            this.$refs.draggableContainer.style.top =
                this.$refs.draggableContainer.offsetTop -
                this.positions.movementY +
                "px";
            this.$refs.draggableContainer.style.left =
                this.$refs.draggableContainer.offsetLeft -
                this.positions.movementX +
                "px";
        },
        closeDragElement() {
            this.positions.active = false;
            document.onmouseup = null;
            document.onmousemove = null;
        },
    },
};
</script>
<style scoped>
.modal_card_box {
    background-color: #ececec;
    /* padding: 10px; */
    border: 1px solid lightgray;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    z-index: 51;
    transition: all 0.25s ease-in-out 0s;
}

.header_modal_box {
    display: flex;
    cursor: grab;
    text-align: center;
    font-size: 20px;
    margin-top: 0;
    padding: 3px;
    border-bottom: 1px solid gray;
}

.header_modal_box > span {
    flex-grow: 1;
}

.grabble {
    cursor: grabbing;
}

.modal_area {
    display: flex;
    flex-direction: column;
    padding: 10px;
}

.item_modal_list {
    padding: 5px;
    background: white;
    border: 1px solid gray;
    min-height: 150px;
    overflow: auto;
    max-height: 300px;
}

.item_modal_row {
    cursor: pointer;
    user-select: none;
    transition: all 0.25s ease-in-out 0s;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.bottom_buttons {
    display: flex;
    justify-content: flex-end;
}

.button {
    border: 1px solid gray;
}

.select_box_input {
    flex-grow: 1;
    margin-bottom: 5px;
    height: 30px;
    outline: none;
}

.modal_background {
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    width: 100%;
    height: 100%;
    inset: 0;
    transform-origin: right bottom;
    /* background-color: rgb(221 221 221 / 30%); */
}

.fa-times {
    cursor: pointer;
    padding: 5px;
}

.fa-times:hover {
    opacity: 0.89;
}
</style>
