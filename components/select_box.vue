<template>
    <div>
        <transition name="zoom-out">
            <div v-if="data.isactive">
                <div
                    class="modal_background"
                    ref="draggableContainer"
                    id="draggable-container"
                >
                    <div
                        class="modal_card_box mat_des_shadow"
                        :style="{width: data.width + 'px'}"
                    >
                        <div
                            class="header_modal_box"
                            :class="{grabble: positions.active}"
                            id="draggable-header"
                            @mousedown.capture="dragMouseDown"
                        >
                            <span>{{ data.header_modal }}</span>
                            <i
                                @click="data.isactive = false"
                                class="fas fa-times"
                            ></i>
                        </div>
                        <div class="modal_area">
                            <input
                                v-model="data.filter_text"
                                v-if="data.filter"
                                @input="$emit('select_box_filter')"
                                placeholder="Поиск"
                                class="select_box_input"
                                type="search"
                            />
                            <div
                                class="item_modal_list"
                                :style="{
                                    'min-height':
                                        data.min_height != undefined
                                            ? data.min_height + 'px'
                                            : 150 + 'px',
                                }"
                            >
                                <div
                                    v-if="
                                        typeof data.multi == 'undefined' ||
                                        !data.multi
                                    "
                                    @click="
                                        $emit('select_item', (cur_item = item))
                                    "
                                    @dblclick="$emit('confirm_action', item)"
                                    class="item_modal_row"
                                    v-for="(item, index) in filtered_items"
                                    :key="index"
                                    :class="{
                                        choosen_par:
                                            data.active_item == item.name,
                                    }"
                                >
                                    {{ item.name }}
                                </div>
                                <div
                                    v-if="data.multi"
                                    @click="$emit('select_item', item)"
                                    class="item_modal_row"
                                    v-for="(item, index) in filtered_items"
                                    :key="index"
                                    :class="{
                                        choosen_par:
                                            data.active_item.indexOf(item) !=
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
                                        $emit(
                                            'confirm_action',
                                            cur_item,
                                            data.closeOnConfirm ? (data.isactive = false) : '')">
                                    Ок
                                </b-button>
                                <b-button
                                    size="is-small"
                                    @click="data.isactive = false"
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
export default {
    props: ["data"],
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
        default() {
            return this.data.isactive;
        },

        filtered_items() {
            if (this.data.filter_text != null) {
                if (this.data.filter_text.length > 0) {
                    return this.data.items.filter((e) => {
                        return e.name
                            .toLowerCase()
                            .includes(this.data.filter_text.toLowerCase());
                    });
                }
            }
            return this.data.items;
        },
    },

    watch: {
        default() {
            if (!this.data.isactive) {
                this.cur_item = [];
                this.data.filter_text = "";
                this.data.active_item = [];
            }
        },
    },

    methods: {
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
    z-index: 30;
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
