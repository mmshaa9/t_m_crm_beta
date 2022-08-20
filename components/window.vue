<template>
    <div>
        <transition name="zoom-out" mode="out-in">
            <div v-if="obj.isactive" @mousedown="$emit('active_window', obj)">
              <div class="modal_card_box_item" ref="draggableContainer" :style="{top: center}" :class="{super: obj.item_id == cur_id || obj.id == cur_id, resize: obj.resize}">
                  <div class="header_modal_box_item" :class="{'grabble': positions.active, active_header: obj.item_id != cur_id && obj.id != cur_id}" id="draggable-header" @mousedown.capture="dragMouseDown">
                  <span>{{obj.header_modal}}</span>
                  <!-- <i @click="$emit('minimize', obj)" class="fas fa-window-minimize"></i> -->
                  <i @click="minimize" class="fas fa-window-minimize hidden_element"></i>
                  <i v-if="!full_width" @click="set_full_width" class="far fa-window-maximize"></i>
                  <i v-else  @click="set_full_width" class="far fa-window-restore"></i>
                  <i @click="$emit('close', obj)" class="fas fa-times"></i>
                  </div>
                  <component v-if="component != null" :key="cur_id" :is="component" v-on="$listeners" :data="prop" :class="{content_full: full_width}">
                    <slot></slot>
                  </component>
                  <!-- <slot v-if="component == null"></slot> -->
                  
              </div>
            </div>
        </transition>

    </div>
</template>

<script>

var offsetWidth_fixed;
// Компонент модального окна c возможность сворачивания, расширения на весь экран и перетаскивания
// На входе принимает объекты obj, prop а так же переменные component и cur_id
// obj отвечает за отображение модального окна, так же возможна настройка высоты и ширины(передача в obj resize так же включает ресайз модального окна)
// prop отвечает за данные, передающиеся в дочерний компонент(соответственно строить данные нужно исходя из данных prop) 
// cur_id отвечает за активность модального окна поверх других окон
// Так же есть функции-слушатели закрытия и сворачивания модального окна
// 
export default({
    props:['obj', 'prop', 'component', 'cur_id'],
    data(){
        return{
            positions: {
                clientX: undefined,
                clientY: undefined,
                movementX: -0,
                movementY: -0,
                active: false,
                move: false              
            },
            full_width: false,
            default_size:{
              width:'',
              height:''
            },
            client_width: 0,
            center:'',
            left:''
        }
    },

    computed:{
      test: function(){
        return this.client_width
      }
    },

    watch:{
      test(news, olds){
        if(this.obj.isactive){
          this.on_center()
        }
      },
    },

    updated(){
      if(this.obj.isactive && !this.positions.move){  
        this.get_center()
        this.get_client_width()
      }
      if(!this.obj.isactive){
        this.positions = {
                clientX: undefined,
                clientY: undefined,
                movementX: -0,
                movementY: -0,
                active: false,
                move: false              
            }
            this.full_width = false
      }
      
    },

    created(){
      window.addEventListener('resize', this.get_client_width);
      // this.get_client_width()
    },

    mounted(){
    },

    methods: {

        minimize: function(){
          this.obj.isactive = false
          // this.$refs.draggableContainer.style.display = 'none'
          if(app.elements.indexOf(this.obj) != -1) return
          app.elements.push(this.obj)
        },

        get_client_width: function(){
          this.client_width =  window.innerWidth
        },

        on_center: function(){
            this.get_default_size()
            this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetParent.offsetWidth -this.default_size.width )/2 + 'px'

        },


        get_default_size: function(){
          this.default_size.width = this.$refs.draggableContainer.lastChild.offsetWidth
          this.default_size.height = this.$refs.draggableContainer.lastChild.offsetHeight
        },

        get_center: function(){
          this.get_default_size()
            this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetParent.offsetHeight - this.default_size.height)/2 + 'px'
            this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetParent.offsetWidth -this.default_size.width )/2 + 'px'
        },

        set_full_width:function(){
            if(!this.full_width){
                this.get_default_size()
            }
            this.full_width = !this.full_width
            
            switch(this.full_width){
                case true:
                    this.$refs.draggableContainer.style.transition = 'all 0.35s ease-in-out 0s'
                    this.$refs.draggableContainer.style.width = 100 + '%'
                    this.$refs.draggableContainer.style.height = window.innerHeight - 62 + 'px'
                    this.$refs.draggableContainer.style.top = '63px'
                    this.$refs.draggableContainer.style.left = '0px'
                    break
                case false:
                    this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetParent.offsetHeight - this.default_size.height)/2 + 'px'
                    this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetParent.offsetWidth -this.default_size.width )/2 + 'px'
                    this.$refs.draggableContainer.style.width =  this.default_size.width + 'px'
                    this.$refs.draggableContainer.style.height = this.default_size.height + 35 + 'px'
                    setTimeout(() => {
                        this.$refs.draggableContainer.style.transition = 'unset'
                    }, 500);
            }
        },
        dragMouseDown: function (event) {
                event.preventDefault()
                this.positions.move = true
                // get the mouse cursor position at startup:
                this.positions.clientX = event.clientX
                this.positions.clientY = event.clientY
                document.onmousemove = this.elementDrag
                document.onmouseup = this.closeDragElement
                offsetWidth_fixed = this.$refs.draggableContainer.offsetWidth // запоминаю ширину в момент захвата
                
            },
            elementDrag: function (event) {
                this.positions.active = true
                event.preventDefault()
                this.positions.movementX = this.positions.clientX - event.clientX
                this.positions.movementY = this.positions.clientY - event.clientY
                this.positions.clientX = event.clientX
                this.positions.clientY = event.clientY
                // set the element's new position
                this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px'
                this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px'                
                this.$refs.draggableContainer.style.width = offsetWidth_fixed + 'px' // устанавливаю исходную величину
                
                if(parseInt(this.$refs.draggableContainer.style.top) <= 63){
                  this.$refs.draggableContainer.style.top = '63px'
                }
                if(parseInt(this.$refs.draggableContainer.style.left) <= 0){
                  this.$refs.draggableContainer.style.left = '0px'
                }
                if(parseInt(this.$refs.draggableContainer.style.left) >= (this.$refs.draggableContainer.offsetParent.offsetWidth -this.default_size.width) ){
                  this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetParent.offsetWidth - this.default_size.width) + 'px'
                }
                if(parseInt(this.$refs.draggableContainer.style.top) > (this.$refs.draggableContainer.offsetParent.offsetHeight - this.default_size.height - 35)){
                  this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetParent.offsetHeight - this.default_size.height - 35) + 'px'
                }
                
            },
            closeDragElement () {
                this.positions.active = false
                document.onmouseup = null
                document.onmousemove = null
            }
    }
})
</script>

<style scoped>
.modal_card_box_item {
    background-color: #ececec ;
    /* padding: 10px; */
    border: 1px solid gray;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    z-index: 30;
    /* width: 1000px;
    height: 70%; */
    overflow: hidden;
    position: absolute;
    /* top: 124px;
    left: 424px; */
    box-shadow: rgb(0 0 0 / 16%) 0px 3px 6px, rgb(0 0 0 / 23%) 0px 3px 6px;
}

.header_modal_box_item {
  display:flex;
  cursor: grab;
  text-align: center;
  font-size: 20px;
  margin-top: 0;
  padding: 3px; 
  background: lightgray;
  border-bottom: 1px solid gray;
  max-height: 35px;
  min-height: 35px;
  overflow: hidden;
  align-items: center;
}

.header_modal_box_item>span{
  flex-grow: 1;
  text-align: left;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  width: 1px;
  margin-left: 5px;
}

.grabble{
  cursor: grabbing
}
.fa-times{
  cursor: pointer;
  padding: 5px;

}

.fa-times:hover{           
  opacity: 0.89;
}

.fa-window-restore{
  cursor: pointer;
  padding: 5px;
  font-size: 16px

}
.fa-window-restore{             
  opacity: 0.89;
}

.fa-window-minimize{
  cursor: pointer;
  padding: 5px;
  font-size: 16px

}
.fa-window-minimize{             
  opacity: 0.89;
}


.fa-window-maximize{
  cursor: pointer;
  padding: 5px;
  font-size: 16px

}
.fa-window-maximize{
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


.super{
    z-index: 50;
    
}

.active_header{
    opacity: 0.7;
}

.resize{
  resize: both;
}

.content_full{
  width: 100% !important;
  height: 100% !important;
}

.centered{
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  display: flex;
    align-items: center;
    justify-content: center;
}
</style>