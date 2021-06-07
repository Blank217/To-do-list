<template>
   <!-- <div class="tick"><i class="far fa-check-circle"></i></div>-->
    <div v-if="!editing" @dblclick="$emit('toggle-reminder',task.id) " 
    :class ="[task.reminder ? 'reminder' : '', 'task']">
        
        <div class="containerCheckbox"><input type="checkbox" v-model="completed" @change="doneEdit"> </div>
        <h3 :class="{ completed : completed }">
            
          {{text}}
           
           <div class="icons">
                <i @click="editTask()" class="fa fa-edit"></i>

                <i @click="deleteTask()" class="fas fa-times" ></i>
            </div>
        </h3>
        <p :class="{ completed : completed }">{{day}}</p>
        
        
    </div>
    <div v-else class='task' > <input class='h3' type="text" v-model="text"  
    @keyup.enter="doneEdit" @keyup.esc="cancelEdit" v-focus> <br>
    <input class='h4' type="text" v-model="day" 
    @keyup.enter="doneEdit"  >  
    </div>
   
</template>

<script>
export default {
    name: 'Task',
    props: {
        task: Object,
                   
        checkAll: Boolean,
      
    
    },
    data () {
    
  return {
    'id': this.task.id,
    'text': this.task.text,
    'day': this.task.day,
    'reminder': this.task.reminder,
    'editing': this.task.editing,
    'beforeEditCache': '',
    'completed': this.task.completed,
    'deleted': this.task.deleted,
   }
  },
  
  directives: {
  focus: {
      mounted(el) {
      el.focus()
    }
  } /* use v-focus on input tag but for mine case i cant v-focus as i have two stimutanous inputs to be open then how ah??? erm so remove @blur solve the problem but now need enter to escape the focus*/
},
  watch: {
    checkAll () {
       if (this.checkAll) {
         this.completed = true
         this.$emit('check', this.completed)
         alert('working')
       } else {
         this.completed = this.task.completed
         this.$emit('check', this.completed)
         alert('working')
      }
      

      /*this.completed = this.checkAll ? true : this.task.completed*/
      
    }
  },
  methods:{
    editTask (){
      this.beforeEditCache = this.text
      this.editing = true
    },
    doneEdit (){
      if (this.text.trim() == ''){
        this.text =this.beforeEditCache
      }
      this.editing = false
      this.$emit('finishedEdit', {
        
          'id': this.id,
          'text': this.text,
          'day': this.day,
          'reminder': this.reminder,
          'editing': this.editing,
          'completed': this.completed,
          'deleted': this.deleted,

        }
      )
      
    },
    cancelEdit (){
      this.text = this.beforeEditCache
      this.editing = false
    },
  deleteTask (id) {
    this.$emit('delete-task',id)
    this.deleted = true
  }

  },
  
  
}


</script>

<style scope>
.fas {
  color: red;
}

.task {
  background: #f4f4f4;
  margin: 5px;
  padding: 10px 20px;
  cursor: pointer;
  
}
.task.reminder {  /* change this to a button rick*/
  border-left: 5px solid green;
}
.task h3 {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.tick{
    float: left;
    margin-left: 10px;
    margin-bottom: 25px;
    font-size: 35px;
}

.fa{
    margin:10px;
    color: silver;
}
.h3 {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-weight: bold;
  font-size: 18px;
  font-family: 'Poppins', sans-serif;
}
.h4 {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  font-family: 'Poppins', sans-serif;
}
.containerCheckbox {
  float:left;
  justify-content: end;
  margin: 7px;
}





</style>