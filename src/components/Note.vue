<template>
  <div ref="draggableContainer" class="draggable-container">
    <div class="draggable-header" @mousedown="dragMouseDown">
      <div class="delete-note" @click="deleteNote"><svg height="24px" viewBox="0 -192 469.33333 469" width="15px" xmlns="http://www.w3.org/2000/svg"><path d="m437.332031.167969h-405.332031c-17.664062 0-32 14.335937-32 32v21.332031c0 17.664062 14.335938 32 32 32h405.332031c17.664063 0 32-14.335938 32-32v-21.332031c0-17.664063-14.335937-32-32-32zm0 0"/></svg></div>
      <div class="date-text"><slot name="header"></slot></div>
    </div>
    <div class= "content">
      <span 
        class="input" 
        ref="editable"
        role="textbox" 
        contenteditable>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Note',
  data: function () {
    return {
      positions: {
        clientX: undefined,
        clientY: undefined,
        movementX: 0,
        movementY: 0
      }
    }
  },
   props: {
    value: {
      type: String,
      default: '',
    },
  },
  computed: {
    listeners() {
      return { ...this.$listeners, input: this.onInput };
    },
  },
  mounted() {
    this.$refs.editable.innerText = this.value;
  },
  methods: {
    dragMouseDown: function (event) {
      event.preventDefault()
      // get the mouse cursor position at startup:
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      document.onmousemove = this.elementDrag
      document.onmouseup = this.closeDragElement
    },
    elementDrag: function (event) {
      event.preventDefault()
      this.positions.movementX = this.positions.clientX - event.clientX
      this.positions.movementY = this.positions.clientY - event.clientY
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      // set the element's new position:
      this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px'
      this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px'
    },
    closeDragElement () {
      document.onmouseup = null
      document.onmousemove = null
    },
    deleteNote(){
      this.$emit('delete-note');
    },
    onInput(e) {
      this.$emit('input', e.target.innerText);
    },
  }
}
</script>

<style lang="scss">
.draggable-container {
  position: absolute;
  z-index: 9;
  background-image: url("../assets/note.png");
  background-size: contain;
  background-repeat: no-repeat;
  min-width: 14rem;
  max-width: 14rem;
  height:auto;
}
.draggable-header {
  z-index: 10;
  display: flex;
  position: relative;
  flex-direction: column;
  padding-left: 47px;
  justify-content: center;
  height: 50px;
  cursor: move;
}
.content{
  padding: 0px 5px 0px 30px;
  height: 15rem;
  overflow: hidden;
  text-align: left;
}

.delete-note{
  position: absolute;
  right: 0;
  cursor: pointer;
  height: 25px;
  width: 25px;
  top: 8px;
  svg{
    position: absolute;
    right: 7px;
    top: -6px;
    fill: gray;
  }
}
.date-text{
  position: absolute;
  top: 9px;
  font-size: 9.5px;
  font-weight: 600;
  color: #706d6d;
}
</style>
