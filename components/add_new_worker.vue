<template>
    <!-- <section>
        <div class="">
            <form action="">
                <div class="modal-card">
                    <header class="modal-card-head">
                        <p class="modal-card-title">Сотрудник</p>
                        <button
                            type="button"
                            class="delete"
                            @click="$emit('close')"/>
                    </header>
                    <section class="modal-card-body">
                        <div class=" top_card">
                            <div class="left_side">
                                <b-field horizontal label="Код" custom-class="field_label">
                                    <b-input
                                        type="text"
                                        placeholder="Код"
                                        disabled>
                                    </b-input>
                                </b-field>

                                <b-field horizontal label="ФИО" custom-class="field_label">
                                    <b-input
                                        type="text"
                                        :value="name"
                                        placeholder="Фио"
                                        required>
                                    </b-input>
                                </b-field>

                                <b-field horizontal label="Отдел" custom-class="field_label">
                                    <b-select expanded label="Отдел" v-model="depart">
                                        <option v-for="depart in departs" :value="depart.Код">{{depart.Отдел}}
                                        </option>
                                    </b-select>
                                </b-field>

                                <b-field horizontal label="Группа" custom-class="field_label">
                                    <b-select expanded label="Группа" v-model="group">
                                        <option v-for="group in groups" :value="group.Код">{{group.группа}}
                                        </option>
                                    </b-select>
                                </b-field>
                                <b-field horizontal label="Руководитель отдела" custom-class="field_label">
                                    <b-select expanded label="Руководитель отдела" v-model="depart_dir">
                                        <option :value="true">Да</option>
                                        <option :value="false">Нет</option>
                                    </b-select>
                                </b-field>
                            </div>
                            <div class="right_side">
                                <div id="preview" class="pre_show_image">
                                    <img src="">
                                </div>
                                    <b-button class="cancel_button" @click='select_photo' size="is-small">Выбрать фото</b-button>
                            </div>
                        </div>
                        <div class="bold">Контактная информация</div>
                        <b-field horizontal label="Телефон" custom-class="field_label"> 
                            <b-input
                                type="text"
                                :value="number"
                                placeholder="Телефон"
                                required>
                            </b-input>
                        </b-field>
                        <b-field horizontal label="Почта" custom-class="field_label">
                            <b-input
                                type="text"
                                :value="mail"
                                placeholder="Почта"
                                required>
                            </b-input>
                        </b-field>
                        <div class="bold">Оклад и режим</div>
                        <b-field horizontal label="Оклад" custom-class="field_label">
                            <b-input
                                type="text"
                                v-model="salary"
                                placeholder="Оклад"
                                required>
                            </b-input>
                        </b-field>
                        <b-field horizontal label="Режим" custom-class="field_label">
                            <b-input
                                type="text"
                                v-model="hours"
                                placeholder="Режим"
                                required>
                            </b-input>
                        </b-field>

                        <div class="bold">Авторизация </div>
                        <b-field horizontal label="Через программу" custom-class="field_label">
                            <b-select label="" v-model="crm_enter">
                                <option :value="true">Да</option>
                                <option :value="false">Нет</option>
                            </b-select>
                        </b-field>
                        <b-field horizontal label="Через терминал">
                            <b-select label="" v-model="terminal_enter" custom-class="field_label">
                                <option :value="true">Да</option>
                                <option :value="false">Нет</option>
                            </b-select>
                        </b-field>
                        
                    </section>
                    <footer class="modal-card-foot">
                        <b-button
                            label="shis"
                            @click="open_box" />
                        <b-button
                            label="Ок"
                            type="is-primary" />
                    </footer>
                </div>
            </form>
                        <select-box :data="{isactive: isactive, width: 540, filter: true, header_modal: 'sелект бокс'}"></select-box>
        </div>
    </section> -->
    <div>
        <div class="text">
            <span class="title">window minimize animation</span>
            <span class="name">by lewi hussey</span>
        </div>

        <div class="window">
            <nav class="window-controls">
                <div class="maximize">mail</div>
                <div class="close"></div>
                <div class="minimize" @click="hide"> asdasd</div>
            </nav>
            
            <aside class="window-sidebar">
                <div class="content-block"></div>
                <div class="content-block"></div>
                <div class="content-block"></div>
                <div class="content-block"></div>
            </aside>
            
            <section class="window-content"></section>
        </div>

        <div class="bg"></div>
    </div>
</template>

<script>

    module.exports = {
        data(){
            return {
                name: '',
                depart: '',
                depart_dir: '',
                group:'',
                number: '',
                mail: '',
                salary: '',
                hours: '',
                image: '',
                crm_enter:'',
                terminal_enter:'',
                url: app.$store.getters.api_url_new,
                departs: [],
                groups:[],
                header_modal: '',
                isactive: false,
                asd: false
            }
        },

        components: {
            'select-box': select_box
        },

        created(){
            this.load_lists()
        },

        methods: {

            hide: function(){
                this.asd = !this.asd
                document.body.classList.add('minimized')
            },

            load_lists: function(){
                axios
                .get(this.url, {params: {cmd: 'load_org_workers_types',  t: new Date()}})
                .then((response) =>{
                    this.departs = response.data.departs
                    this.groups = response.data.groups
                }) 
            },

            open_box(){
                this.isactive = true
            },

            

            cardModal() {
                this.$buefy.modal.open({
                    parent: this,
                    component: ModalForm,
                    hasModalCard: true,
                    customClass: 'custom-class custom-class-2',
                    trapFocus: true
                })
            },

            select_photo: function(){
                selectFile( (files) => {
                    // for (let i = 0; i < files.length; i++) {
                    //     element = files[i]
                    //     console.log(element)
                    //     if(!element.type.match('image.*')){
                    //         app.modal_alert('', 'Необходимо загрузить изображение', 'Ок')
                    //         return
                    //     }else{
                    //         // const url = window.URL.createObjectURL(new Blob([element]));
                    //         // this.image = url
                    //         // var reader = new FileReader()
                    //         reader.readAsDataURL(element)
                    //         console.log(reader.readAsDataURL(element))
                    //         // this.image = createImageBitmap(element)
                    //     }
                    // }
                    for (var i = 0; i < files.length; i++) {
                        var file = files[i];

                        if (!file.type.startsWith('image/')){ app.modal_alert('', 'Необходимо загрузить изображение', 'Ок') 
                            files = []
                            return
                        }

                        var img = document.createElement("img");
                        img.classList.add("obj");
                        img.file = file;
                        preview.appendChild(img); // Предполагается, что "preview" это div, в котором будет отображаться содержимое.

                        var reader = new FileReader();
                        reader.onload = (function(aImg) { return function(e) { aImg.src = e.target.result; }; })(img);
                        reader.readAsDataURL(file);
                    }
                }, false)
            },

            save_worker: function(){

            }
        }
    }
</script>

<style scoped >
/* .top_card{
    display: flex;
    width: 100%;
}

.modal-card-body{
    padding: 10px
}

.left_side{
    width: 60%;
}

.right_side{
    width: 40%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
}

.pre_show_image {
    border: 1px solid lightgray;
    height: 100%;
    margin: 0px 0px 15px 10px;
}

.cancel_button {
    margin-left: 10px;
} */


*{
	box-sizing: border-box;
	padding: 0;
	margin: 0;
	font-family: sans-serif;
	font-weight: 300;
}

.bg{
	background: linear-gradient(to top right, #060628, #1F245A, #682359);
	position: fixed;
	top: 0;
	left: 0;
	width: 100vw;
	height: 100vh;
}

.window{
	position: fixed;
	top: 50%;
	left: 50%;
	width: 700px;
	height: 400px;
	transform: translate(-50%, -50%);
	background-color: white;
	border-radius: 4px;
	z-index: 5;
	box-shadow: 0 20px 40px rgba(0, 0, 0, .4);
	overflow: hidden;
	
	transition:
		top .5s 0s cubic-bezier(.1, 1.2, .3, 1),
		transform .5s 0s cubic-bezier(.1, 1.2, .3, 1),
		width .5s .5s cubic-bezier(.1, 1.2, .3, 1),
		opacity .3s;
	
	.window-controls{
		position: absolute;
		top: 0;
		left: 0;
		z-index: 9;
		height: 30px;
		width: 60px;
		
		.maximize{
			background-color: rgb(232, 234, 236);
			position: absolute;
			top: -30px;
			opacity: 0;
			left: 0;
			z-index: 9;
			width: 200px;
			height: 30px;
			text-align: center;
			line-height: 30px;
			color: rgb(180, 185, 190);
			cursor: pointer;
			
			transition:
				opacity .3s .5s,
				top 0s .8s;
		}
		
		.close,
		.minimize{
			opacity: .5;
			cursor: pointer;
			
			&:hover{
				opacity: .7;
			}
			
			&:active{
				opacity: 1;
			}
		}
		
		.close{
			width: 30px;
			height: 30px;
			float: left;
			
			&:before,
			&:after{
				position: absolute;
				content: "";
				width: 12px;
				height: 2px;
				background-color: black;
				position: absolute;
				border-radius: 2px;
				
				top: 14px;
				left: 9px;
			}
			
			&:before{
				transform: rotate(45deg);
			}
			
			&:after{
				transform: rotate(-45deg);
			}
		}
		
		.minimize{
			width: 30px;
			height: 30px;
			float: left;
			position: relative;
			
			&:before,
			&:after{
				position: absolute;
				content: "";
				width: 10px;
				height: 2px;
				background-color: black;
				position: absolute;
				border-radius: 2px;
				
				top: 14px;
				left: 10px;
			}
			
			&:before{
				transform: translateX(-3px) rotate(45deg);
			}
			
			&:after{
				transform: translateX(3px) rotate(-45deg);
			}
		}
	}
	
	.window-sidebar{
		width: 200px;
		height: 400px;
		background-color: rgb(232, 234, 236);
		position: absolute;
		top: 0;
		left: 0;
		padding-top: 30px;
		z-index: 4;
		
		.content-block{
			height: 60px;
			width: 100%;
			position: relative;
			border-top: 1px solid rgba(0, 0, 0, .05);
			
			&:before{
				position: absolute;
				content: "";
				width: 40px;
				height: 40px;
				border-radius: 50%;
				background-color: black;
				top: 10px;
				left: 10px;
				opacity: .05;
			}
			
			&:after{
				position: absolute;
				content: "";
				width: 120px;
				height: 10px;
				background-color: black;
				top: 25px;
				left: 60px;
				opacity: .05;
				border-radius: 5px;
			}
		}
	}
	
	.window-content{
		position: absolute;
		top: 0;
		right: 0;
		width: 500px;
		height: 400px;
		
		&:before{
			position: absolute;
			content: "";
			width: 460px;
			height: 200px;
			background-color: black;
			top: 20px;
			left: 20px;
			opacity: .05;
			border-radius: 5px;
		}
		
		&:after{
			position: absolute;
			content: "";
			width: 380px;
			height: 10px;
			background-color: black;
			top: 240px;
			left: 60px;
			opacity: .05;
			border-radius: 5px;
		}
	}
}

.minimized{
	.window{
		top: 100%;
		transform: translate(-50%, -30px);
		width: 200px;
		opacity: .5;
		
		&:hover{
			opacity: .75;
		}
		
		.maximize{
			top: 0;
			opacity: 1;
			
			transition:
				opacity .3s .5s,
				top 0s .5s;
		}
	}
}

.text{
	position: fixed;
	top: 50%;
	left: 50%;
	color: white;
	transform: translate(350px, -200px);
	z-index: 10;
	padding-left: 20px;
	
	.title{
		font-size: 20px;
		line-height: 20px;
		display: block;
		margin-bottom: 5px;
	}
	
	.name{
		display: block;
		opacity: .5;
	}
}
</style>