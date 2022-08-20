<template>
    <div class="background_box"  v-if="worker.graf.visible">
        <div class="graph_mini_card">
            <div class="head_staff">
                <div class="graph_mini_card_head_text">Выбрать</div>
                <div class="graph_mini_card_close_butt">
                    <button class="close_btn" @click="worker.graf.visible=false">&#10006;</button>
                </div>
            </div>
            <div class="graph_mini_card_content" v-for="graf in grafs">
                <div class="graph_mini_card_content_line" :class="{bold: active == graf, active_row: active == graf}"  @click="choose_graf(graf)">
                    <div class="graph_mini_card_content_line_text">{{graf.grafs_name}}</div>
                </div>
            </div>
            <div class="graph_mini_card_butt">
                <button class="graph_mini_card_btn" @click="action">Ок</button>
                <button class="graph_mini_card_btn"  @click="worker.graf.visible=false">Отмена</button>
            </div>
        </div>
    </div>
</template>

<script>

    module.exports = ({
        props:['conceal'],
        data(){
            return {
              grafs:[
                  {
                    grafs_name: 'Поставить смену',
                  },
                  {
                    grafs_name: 'Удалить смену',
                  },
                  {
                    grafs_name: 'Поставить отпуск',
                  },
                  {
                    grafs_name: 'Поставить приход',
                  },
                  {
                    grafs_name: 'Поставить уход',
                  },
              ],
              active: null,
            }  
        },  
        methods: {
            choose_graf(graf){
                this.active = graf
            },
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
            },

        },
    })
</script>

<style scoped>
    .background_box{
        display: flex;
        justify-content: center;
        align-items: center;
        top: 0;
        left: 0;
        height: 100%;
        width: 100%;
        background: rgba(0, 0, 0, 0.70);
        position: fixed;
        z-index: 50;
    }
    .head_staff {
        display: flex;
        justify-content:space-between;
        align-items: center;
        padding: 2px 8px;
        font-weight: 400 !important;
    }
    .close_btn{
        background: local;
        border: none;
        padding:  3px 5px;
    }
     .close_btn:hover{
        background: red;
        border-radius: 3px;
    }
    button{
        cursor: pointer;
    }
    .active_row{
        background: gainsboro;
    }
    .graph_mini_card{
        margin-top: 5%;
        width: 300px;
        background: white;
        height: max-content;
        border: 1px solid gray;
        position: absolute;
    }
    .head_staff{
        border-bottom: 1px solid  #929397;
        background: rgb(243, 243, 243);
    }
    .graph_mini_card_head{
        display: flex;
        justify-content: space-between;
        align-items: center;
        background: gainsboro;
        border-bottom: 1px solid  #929397;
        padding: 4px;
    }
    
    .graph_mini_card_content{
        padding: 5px;
    }
    .graph_mini_card_content_line{
        padding-bottom: 3px;
        font-weight: 400;
        cursor: pointer;
    }
    .graph_mini_card_butt{
        display: flex;
        justify-content: flex-end;
        background: rgb(243, 243, 243);
        border-top: 1px solid  #929397;
        padding: 5px;
    }
    .graph_mini_card_btn{
        padding: 3px 15px;
        margin-right: 5px;
    }
    .click_graf_class_active{
        background: #929397;
    }

</style>