<template>
    <div class="page_staff_award_settings">
        <div class="page_staff_award_settings_content">
            <div class="award_settings_line">
                <div class="award_settings_line_text">Код</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">План</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">Оклад 1</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">Процент 1</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">Оклад 2</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">Процент 2</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">Начало</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">Конец</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_line">
                <div class="award_settings_line_text">Порог</div>
                <div class="award_settings_line_inp">
                    <input type="text" class="settings_line_inp tm_input">
                </div>
            </div>
            <div class="award_settings_butt">
                <button class="settings_line_btn button" @click="action">Сохранить</button>
                <button class="settings_line_btn button" @click="$parent.obj.isactive = false">Отмена</button>
            </div>
        </div>
    </div>
</template>

<script>

    module.exports = ({
        props:['conceal', 'worker'],
        data(){
            return {
               
            }  
        },
        methods: {
            action(){
                var bodyFormData = new FormData()
                bodyFormData.append('cmd', '')
                axios({
                        method: 'post',
                        url: this.url,
                        data: bodyFormData,
                        headers: {'Content-Type': 'multipart/form-data' },
                        }) 
                .then((response)=>{
                    console.log(response.data)
                })
            }
        }    
    })
</script>

<style scoped>
    button{
        cursor: pointer;
    }
    .page_staff_award_settings {
        display: flex;
        flex-direction: column;
        width: 400px;
        background: white;
    }
    .award_settings_line {
        display: flex;
        justify-content: space-between;
        padding: 7px 10px;
    }
    .award_settings_line_text {
        width: 30%;
    }
    .award_settings_line_inp {
        width: 70%;
    }
    .settings_line_inp {
        width: 100%;
        height: 25px;
    }
    .settings_line_btn {
       height: 30px;

    }
    .award_settings_butt{
        display: flex;
        justify-content: end;
        padding: 5px;
        background: gainsboro;
        border-top: 1px solid lightgray;
        margin-top: 5px;
    }

</style>