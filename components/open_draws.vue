<template>
    <div :title="draw_title(dop.SW_Формат ?? dop.sw_format ?? dop.detail_format)">
        <i :class="{disabled: is_disabled}" class="fas fa-drafting-compass draw_icons_hover" @click.stop="open_draw(dop)" ></i>
        <div class="menu_sett">
            <ul v-if="Array.isArray(dop.draws) && dop.draws.length > 0"    class="context_menu mat_des_shadow" ref="contextMenu" >
                <li class="close_card header_card">
                    Закрыть окно<i @click="hide_draws(dop)" title="Закрыть окно" class="fas fa-times"></i>
                </li>
                <li class="cont_menu" @click="open_draw_link(draw)" v-for="(draw, index) in dop.draws" :key="index">
                    {{(dop.наименование2 ?? dop.namezakup ?? dop.name ?? dop.наименование3 ?? dop.SW_ИмяФайла ?? dop.detail_name) + "_0"+(index + 1) + ".jpg"}}<i @click.stop="dowload_draws(draw, 'one', draw)" title="Скачать файл" class="draw_icons_hover fas fa-arrow-alt-circle-down"></i>
                </li>
                <li class="close_card">
                    Скачать все файлы<i  @click="dowload_draws(dop, 'all')" title="Скачать всё" class="draw_icons_hover fas fa-arrow-alt-circle-down"></i>
                </li>
            </ul>
        </div>
    </div>

</template>

<script>
import axios from 'axios'
import * as my_f from "@/my"
import { mapActions } from 'vuex'

export default ({

    components:{
    },
    props:['dop'],
    data(){
        return{
            url:this.$store.getters.api_url_new,
            alert:{
                isactive: false,
                onconfirm: () =>{}
            },
        }
    },
    computed:{
        is_disabled(){
            return (this.dop.SW_Формат ?? this.dop.sw_format ?? this.dop.detail_format) == 'БЧ' || (this.dop.SW_Формат ?? this.dop.sw_format ?? this.dop.detail_format) == '' || (this.dop.SW_Формат ?? this.dop.sw_format ?? this.dop.detail_format) == null
        }
    },

    methods:{
        
        ...mapActions([
            'show_alert'
        ]),

        open_draw(par){
            axios
                .get(this.url, { params: {cmd: "open_draw", part_id: par.Код || par.detail_id || par.КомплектующаяКод, t: new Date() }})
                .then((response) =>{
                    console.log(response.data)
                    if(Array.isArray(response.data) && response.data.length > 0){
                        console.log('Массив')
                        this.$set(par, "draws", response.data)
                    }else{
                        if(response.data !== [] && response.data.length > 0){
                            window.open(response.data)
                        }else{
                            this.show_alert({ 
                                text: "Не удалось открыть чертеж.", 
                                action: ()=> {
                                    return
                                }
                            })
                        }
                    }
                })
        },
        
        open_draw_link(link){
            my_f.file_operation("open", link)
        },

        dowload_draws(par, mode, link = null){
            switch(mode){
                case "one":
                    my_f.file_operation("load", par)
                    break
                case "all":
                    par.draws.forEach(el => {
                        my_f.file_operation('load', el)
                    });
                    break
            }
        },

        hide_draws(par){
            par.draws = []
        },

        draw_title(par){
            if(par == 'БЧ' || par == '' || par== null){
                return "Нет чертежа"
            }else{
                return "Открыть чертеж"
            }
        },
    }
})
</script>

<style scoped>

.draw_icons_hover{
    opacity: .7;
}

.draw_icons_hover:hover{
    box-shadow: antiquewhite;
    text-shadow: 0px 0px 10px darkorange;
    cursor: pointer;
}

.context_menu{
    position: absolute;
    background-color: #fff;
    border-radius: 10px;
    padding-bottom: 0.5rem;
}

.cont_menu{
    text-align: inherit;
    width: 100%;
    color: rgb(74, 74, 74);
    font-size: 0.875rem;
    line-height: 1.5;
    padding: 0.375rem 1rem;
    cursor: pointer;
    border: 1px solid lightgray;
    margin-top: -1px;
    white-space: normal;
    display: flex;
    align-items: center;
}

.cont_menu>.draw_icons_hover{
    padding-left: 3px;
}

.cont_menu:hover{
    background-color:#f5f5f5;
    color:#0a0a0a
}

.menu_sett{
    white-space: normal;
}

.close_card{
    display: flex;
    align-items: center;
    justify-content: flex-end;
    padding-right: 10px;
    cursor: pointer;
}

.header_card{
    background: lightgray;
    padding-left: 5px;
    overflow: hidden;
    border-radius: 10px 10px 0px 0px;
    justify-content: space-between;
}

.close_card>i{
    padding-left: 3px;
}

</style>
    