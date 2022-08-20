<template>
    <div class="demand_row" :class="{
                                    text_green: client.Категория == 'ЗелАктив',
                                    text_light_green:
                                        client.Категория == 'ЗелПассив',
                                    text_red:
                                        client.Категория ==
                                        ('Забытый' || 'В разработке'),
                                    text_fade: client.Категория == 'Отказной',
                                }">
        <div class="demand_header">
            <span>{{"Заявка №"+ client.заявка_код + " от " + client.забрал}}</span><span v-if="all">{{", отдана: " + (client.сотрудник_взял_имя ?? "ЗАЯВКА НЕ РАСПРЕДЕЛЕНА")}}</span>
        </div>
        <div class="client_name bold">
            <span @click.stop="!all ? $parent.open_client_card(client.Код) : false" :title="!all ? 'Открыть карточку клиента с заявками' : ''" :class="{client_open_card: !all}">{{client.название_формат}}</span>
            <div
                v-if="
                    client.Категория !=
                        'Отказной' 
                "
                class="last_date bold" :class="{text_red: date_miss(client.ДСК)}"
            >
                {{ client.Последнее_событие }}
            </div>
        </div>
        <div class="client_adress_last_act">
            {{ client.адрес }}
        </div>
        <spoiler
            :key="client.сообщение"
            :text="client.сообщение"
            show_text="Показать полностью"
            :length="90"
            class="spoiler_text"
            :class="{
                spoiler_text_hr:
                    client.сообщение.length > 0,
            }"
        ></spoiler>
    </div>
</template>

<script>
import spoiler from '@/components/spoiler.vue'
import * as my from '@/my'

export default {
    props:['client', 'all'],
    components:{
        'spoiler': spoiler
    },

    data(){
        return {

        }
    },

    computed:{
    },

    methods:{
        
        date_miss(date){
            return new Date() > new Date(date)
        }
    }
}
</script>

<style scoped>

.client_name {
    display: flex;
    justify-content: space-between;
    cursor: pointer;
}

.demand_header{
    color: gray;
    border-bottom: 1px solid;
    font-size: 12px;
}

.spoiler_text {
    font-style: italic;
    font-size: 14px;
    color: black;
}

.spoiler_text_hr {
    border: 1px solid rgb(0 0 0 / 18%);
    padding: 5px;
    background: rgb(0 0 0 / 4%);
    margin: 5px 0px;
    border-radius: 4px;
}


.client_open_card:hover{
    color: #819ca9;
    transition: all 0.15s ease-in-out;
    text-decoration: underline;
}

.demand_row {
    min-height: 40px;
    margin-bottom: 5px;
    background: white;
    /* color:brown; */
    padding-left: 5px;
    padding-right: 5px;
    border-radius: 5px;
    box-shadow: rgb(0 0 0 / 12%) 0px 1px 3px, rgb(0 0 0 / 24%) 0px 1px 2px;
    transition: all 0.25s ease-in-out;
    overflow: hidden;
}

.demand_row:hover {
    opacity: 1;
    cursor: pointer;
    background-color: rgb(238, 238, 238);
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

</style>
