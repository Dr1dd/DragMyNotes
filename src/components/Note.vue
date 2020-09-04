<template>
  <div ref="draggableContainer" class="draggable-container" @click="setZIndex">
    <div class="draggable-header" @mousedown="dragMouseDown" @mouseup ="setPosition">
      <div class="delete-note" @click="deleteNote"><svg height="24px" viewBox="0 -192 469.33333 469" width="15px" xmlns="http://www.w3.org/2000/svg"><path d="m437.332031.167969h-405.332031c-17.664062 0-32 14.335937-32 32v21.332031c0 17.664062 14.335938 32 32 32h405.332031c17.664063 0 32-14.335938 32-32v-21.332031c0-17.664063-14.335937-32-32-32zm0 0"/></svg></div>
      <div class="date-text"><slot name="header"></slot></div>
    </div>
    <div class= "content">
          <span 
              ref="editable"
              role="textbox" 
              contenteditable
              @input="updateText">
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
      },
      noteObjectArray: JSON.parse(localStorage.getItem('noteArray')),
      noteOnTop: false,
    }
  },
   props: {
    id: Number,
    position: Array
  },
  mounted() {
       this.$refs.draggableContainer.style.top = this.position[1];
       this.$refs.draggableContainer.style.left = this.position[0];
       var noteObject = JSON.parse(localStorage.getItem('noteArray'));
       this.$refs.editable.innerText = noteObject[this.id].text;
  },
  methods: {
    dragMouseDown: function (event) {
      event.preventDefault();
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
    setPosition(){
      var noteObject = JSON.parse(localStorage.getItem('noteArray'));
      noteObject[this.id].position = [this.$refs.draggableContainer.style.left, this.$refs.draggableContainer.style.top];
      localStorage.setItem('noteArray', JSON.stringify(noteObject));
    },
    updateText(e){
      var noteId = e.target.parentNode.parentNode.parentNode.getAttribute('id');
      noteId = noteId.substring(5, noteId.length);
      this.noteObjectArray[noteId].text = e.target.innerText;
      this.noteObjectArray[noteId].date = Date.now();
      localStorage.setItem('noteArray', JSON.stringify(this.noteObjectArray));
    },
    setZIndex(){
      [].forEach.call(document.querySelectorAll('.recent'), function (el) {
          el.classList.remove('recent');
      });
      if(!this.$refs.draggableContainer.classList.contains('recent'))
        this.$refs.draggableContainer.classList.add('recent');
    }
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
  z-index: 0;
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
.recent{
  z-index: 1;
}
</style>
