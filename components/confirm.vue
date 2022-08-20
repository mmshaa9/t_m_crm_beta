<template>
    <div>
        <transition name="zoom-in">
            <div v-if="confirm.isactive" >
                <div class="modal_background" :class="{with_background: confirm.need_back ?? false}" @keyup.esc="confirm.cancel_action()"  ref="draggableContainer" id="draggable-container">
                    <div class="modal_card_box mat_des_shadow" :style="{ 'width': confirm.width + 'px'}" >
                        <div class="header_modal_box" :class="{'grabble': positions.active}" id="draggable-header" @mousedown.capture="dragMouseDown">
                        <span>{{confirm.header_modal}}</span>
                        <i @click="close" class="fas fa-times"></i>
                        </div>
                        <div class="modal_area">
                            <div class="confirm_text">{{confirm.text}}
                            </div>
                        </div>
                        <div class="bottom_buttons">
                            <b-button size="is-small" tabindex="1"  ref="confirm_button" @click.stop.prevent="confirm.confirm_action(), confirm.closeOnConfirm ? confirm.isactive = false: ''">
                              {{confirm.confirm_text}}
                            </b-button>
                            <b-button size="is-small" tabindex="2"   @click="confirm.cancel_action()">
                              {{confirm.cancel_text}}
                            </b-button>
                            <b-button v-if="confirm.cancel_need" size="is-small" @click="close" class="cancel_button">
                              Отмена
                            </b-button>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>

import { mapState, mapActions } from 'vuex'

export default ({
        data: function(){
            return {
              cur_item: '',
              positions: {
                  clientX: undefined,
                  clientY: undefined,
                  movementX: -0,
                  movementY: -0,
                  active: false              }
            }
        },
        computed:{
            ...mapState([
                'confirm'
            ])
        },

		updated(){
			// if(this.confirm.isactive){
				
			// }
			// this.$nextTick( ()=>{	
				if(typeof this.$refs.confirm_button !== 'undefined'){
					this.$refs.confirm_button.$el.focus()	
				}
			// })
		},
    created(){
      if(this.confirm.isactive){
        this.close()
      }
    },

      mounted(){
        
      },

        methods:{
          ...mapActions([
                'close_confirm'
          ]),

          close(){
            console.log('sas')
            this.close_confirm()
          },
          dragMouseDown: function (event) {
                event.preventDefault()
                // get the mouse cursor position at startup:
                this.positions.clientX = event.clientX
                this.positions.clientY = event.clientY
                document.onmousemove = this.elementDrag
                document.onmouseup = this.closeDragElement
            },
            elementDrag: function (event) {
                this.positions.active = true
                event.preventDefault()
                this.positions.movementX = this.positions.clientX - event.clientX
                this.positions.movementY = this.positions.clientY - event.clientY
                this.positions.clientX = event.clientX
                this.positions.clientY = event.clientY
                // set the element's new position:
                // this.$refs.draggableContainer.style.transform = 'translate3d(' + ((this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px,' + (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px, 0)')
                this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px'
                this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px'
            },
            closeDragElement () {
                this.positions.active = false
                document.onmouseup = null
                document.onmousemove = null
            },

        }
    })
</script>

<style scoped>
.modal_card_box {
    background-color: #ececec ;
    /* padding: 10px; */
    border: 1px solid lightgray;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    z-index: 100;
}

.header_modal_box {
  display:flex;
  cursor: grab;
  text-align: center;
  font-size: 20px;
  margin-top: 0;
  padding: 3px; 
  background: whitesmoke;
  border-radius: 10px;
  /* border-bottom: 1px solid gray; */
}

.header_modal_box>span{
  flex-grow: 1;
}

.grabble{
  cursor: grabbing
}

.modal_area{
  display: flex;
  flex-direction: column;
  padding: 10px;
  background: whitesmoke;
  border-bottom: 1px solid lightgray;
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
}

.bottom_buttons{
  display: flex;
  justify-content: flex-end;
  padding: 10px
}

.button {
  border: 1px solid gray
}

.select_box_input{
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

.with_background{
  background-color: rgb(0 0 0 / 30%);
  z-index: 100;
}

.fa-times{
  cursor: pointer;
  padding: 5px;

}

.fa-times:hover{
  opacity: 0.89;
}

.confirm_text{
    /* background: white;
    font-size: 21px;
    border-radius: 6px;
    padding: 5px;
    border: 1px solid lightgray; */
    /* white-space: pre-line;
    clear: both;
    box-shadow: rgb(0 0 0) 0px 0px 2px inset; */
    white-space: pre-line;
}
</style>