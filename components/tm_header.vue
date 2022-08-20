<template>
    <div class="tm_header mat_des_shadow">
        <div class="line">
            <div class="search">
                <input
                    class="inp_search tm_input"
                    type="search"
                    v-model="filter_text"
                    @input="throttle_search"
                    placeholder="Поиск"
                />
            </div>
            <div class="different_butt">
                <slot name="butt"></slot>
            </div>
        </div>
        <slot name="title"></slot>
    </div>
</template>

<script>
import * as my_f from "@/my";
export default {
    props: ["data"],
    data() {
        return {
            filter_text: "",
        };
    },
    mounted: function () {},

    computed:{
        throttle_search(){
            return this.$debounce(this.filter, 1000)
        },
    },

    methods: {
        filter(){
            this.$emit('filter', this.filter_text)
        }
    },
};
</script>

<style scoped>
.tm_header {
    width: 100%;
}
.line {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 45px;
    background: #ececec;
    border: 1px solid lightgray;
    border-bottom: none;
    padding: 5px;
}
.search {
    flex-grow: 1;
    display: flex;
}
.inp_search {
    padding-left: 5px;
    height: 30px;
}
.different_butt {
    display: flex;
    justify-content: space-around;
    align-items: center;
}
.top_list {
    display: flex;
    width: 100%;
    align-items: center;
    justify-content: space-between;
    border: 1px solid lightgray;
    background-color: #ececec;
    font-weight: bold;
    color: #344a57;
    padding: 1px 35px 1px 5px;
}

.top_list > div:nth-child(1) {
    flex-grow: 1;
}
.top_list > div {
    width: 130px;
    -ms-flex-negative: 1;
    flex-shrink: 0;
    flex-grow: 0;
    flex-basis: 1;
    margin: 0;
    padding: 4px 4px;
}
.button {
    height: 30px;
}
</style>
