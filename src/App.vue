<template>
  <div id="app">
    <div class = "note" v-for="(note, index) in noteObjectArray" :key="note.id" :id="'note_'+index">
      <Note v-on:delete-note="deleteNote(index)" :id="index" :position="noteObjectArray[index].position">
        <template slot="header">
          Last modified: {{ formattedDate(note.date) }}
        </template>
      </Note>
    </div>
    <div class="add-note" @click="addNote"><svg viewBox="0 0 448 448"  xmlns="http://www.w3.org/2000/svg"><path d="m408 184h-136c-4.417969 0-8-3.582031-8-8v-136c0-22.089844-17.910156-40-40-40s-40 17.910156-40 40v136c0 4.417969-3.582031 8-8 8h-136c-22.089844 0-40 17.910156-40 40s17.910156 40 40 40h136c4.417969 0 8 3.582031 8 8v136c0 22.089844 17.910156 40 40 40s40-17.910156 40-40v-136c0-4.417969 3.582031-8 8-8h136c22.089844 0 40-17.910156 40-40s-17.910156-40-40-40zm0 0"/></svg></div>
  </div>
</template>

<script>

import Note from './components/Note'
export default {
  name: 'App',
  components: {
    Note
  },
  data(){
    return{
      noteObjectArray: JSON.parse(localStorage.getItem('noteArray')),
      content: '',
    }
  },
  methods:{
    addNote(){
      var newNote = {};
      if(this.noteObjectArray.length ==0) newNote = {id: 0, date: Date.now(), text:'Please enter the note text', position: ['200px','350px']};
      else newNote = {id: this.noteObjectArray.length, date: Date.now(), text:'Please enter the note text', position: ['200px','350px']};
      this.noteObjectArray.push(newNote);
      localStorage.setItem('noteArray', JSON.stringify(this.noteObjectArray));
    },
    deleteNote(index){
      var noteObject = JSON.parse(localStorage.getItem('noteArray'));
      noteObject.splice(index, 1);
      localStorage.setItem('noteArray', JSON.stringify(noteObject));
    },
    formattedDate(date){
      var fullDate = new Date(date);
      var year = fullDate.getFullYear();
      var month = fullDate.getMonth()+1;
      month = (month < 10) ? '0'+month : month;
      var day = fullDate.getUTCDay()-1;
      day = (day < 10) ? '0'+day : day;
      var hour = fullDate.getHours()
      hour = (hour < 10) ? '0'+hour : hour;
      var min = fullDate.getMinutes();
      min = (min < 10) ? '0'+min : min;
  
      var newDateString = year+'/'+month +'/'+day+' '+hour+":"+min;
      return newDateString;
    },
  },

  // created: function () {
  //   window.addEventListener('click', event => {
  //     if (.contains(e.target)){
  //       // Clicked in box
  //     } else{
  //       // Clicked outside the box
  //     }
  //   });

    
  // },

  // destroyed: function () {
  //   window.removeEventListener('click', this.keyDown)

  // }

}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
span{
  outline: none;
  border: none;
  padding: 0;
  height:100%;
  line-height: 117%;
  width: 100%;
  font-size: 0.8em;
  color: gray;
  

}
.add-note{
  position: fixed;
  display: flex;
  flex-direction: column;
  justify-content: center;
  bottom: 25px;
  right: 25px;
  height: 50px;
  width: 50px;
  background: linear-gradient(180deg, rgba(253,255,186,1) 0%, rgba(255,251,0,1) 33%, rgba(84,244,90,1) 33%, rgba(122,255,0,1) 66%, rgba(255,125,125,1) 66%, rgba(254,0,0,1) 100%);
  border-radius: 50%;
  cursor: pointer;
  svg{
    margin: 0 auto;
    height: 50%;
    width: 50%;
    fill: #780000cc;
  }
}
</style>
