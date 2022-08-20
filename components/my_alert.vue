<template>
    <div>
        <transition name="animation-content">
            <div class="alert_back" v-if="alert_data.isactive">
                <transition name="zoom-out">
                    <div class="alert_area">
                        <div class="alert_text">
{{ alert_data.text }}
</div>
                        <div class="alert_button">
                            <b-button
                                @click="close_alert(alert_data.onconfirm)"
                                >{{ alert_data.confirm_text }}</b-button
                            >
                        </div>
                    </div>
                </transition>
            </div>
        </transition>
    </div>
</template>

<script>
import { mapActions } from 'vuex'
export default {
    // props:['alert'],
    data() {
        return {
            alert: {},
        };
    },

    computed: {
        alert_data: function () {
            return this.$store.getters.alert;
        },
    },

    updated() {
        // this.alert = this.alert_data
    },

    created() {
        // this.alert = this.
        if(this.alert_data.isactive){
            this.alert_data.isactive = false
        }
    },

    methods: {

        ...mapActions([
            'show_alert'
        ]),
        
        close_alert: function (callback) {
            this.show_alert({}).then(()=>{
                    callback()
                }
            )
        },
    },
};
</script>

<style scoped>
.alert_back {
    background-color: rgba(10, 10, 10, 0.86);
    z-index: 100;
    width: 100%;
    height: 100%;
    bottom: 0;
    left: 0;
    position: absolute;
    right: 0;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
}

.alert_area {
    width: 400px;
    max-height: 500px;
    background-color: #fff;
    border-radius: 5px;
    overflow: hidden;
}

.alert_text {
    padding: 20px;
}

.alert_button {
    justify-content: flex-end;
    padding: 20px;
    display: flex;
    align-items: center;
    background-color: #f5f5f5;
}

/*...........медиа запрсоы............*/

@media (min-width: 0px) and (max-width: 500px) {
    .modal-card.animation-content {
        width: 60%;
    }
    .media > .media-content > p > div {
        font-size: 0.62em;
    }
}
@media (min-width: 501px) and (max-width: 720px) {
    .modal-card.animation-content {
        width: 70%;
    }
    .media > .media-content > p > div {
        font-size: 0.9em;
    }
}

@media (min-width: 721px) and (max-width: 920px) {
    .modal-card.animation-content {
        width: 80%;
    }
    .media > .media-content > p > div {
        font-size: 1em;
    }
}

@media (min-width: 921px) and (max-width: 1024px) {
    .modal-card.animation-content {
        width: 90%;
    }
    .media > .media-content > p > div {
        font-size: 1.12em;
    }
}
</style>
