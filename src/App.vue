<template>
  <div class="container">
    <Header @toggle-add-task="toggleAddTask" 
    title="To-do List" :showAddTask="showAddTask" />
    <div v-show="showAddTask">
    <AddTask @add-task="addTask"/>
    </div>
    <Tasks @toggle-reminder="toggleReminder"
    :checkAll="!anyRemaining"
    :filter="this.filter"
    @finishedEdit="finishedEdit"
    @delete-task="deleteTask" :tasks="tasks" />


    <div class="extra-container">
     <div>
      <label>
        <input type="checkbox" :checked="!anyRemaining" @change="checkAllTasks"> Check All
      </label>
     </div>
     <div>{{remaining}} items left </div>
   </div>
   
   
    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
        <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
        <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
        <button :class="{active: filter =='showalldeleted' }" @click="filter = 'showalldeleted'">show all deleted </button>
      </div>

      <!--<div>
        <transition name="fade">
        <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
        </transition>
      </div>-->

    </div>
    
  
  </div> 
  
</template>

<script>
import Header from './components/Header'
import Tasks from './components/Tasks'
import AddTask from './components/AddTask'



export default {
  name: 'App',
  components: {
    Header,
    Tasks,
    AddTask,
    
  },
  data (){
    return {
      filter: 'all',
      DeletedTasks: [],
      
      
      tasks: [
        {
      "id": "1",
      "text": "Doctors Appointment",
      "day": "March 5th at 2:30pm",
      "reminder": false ,
      "editing": false,
      "completed": false,
      "deleted": false,
    },
    {
      "id": "2",
      "text": "Meeting with boss",
      "day": "March 6th at 1:30pm",
      "reminder": false ,
      "editing": false,
      "completed": false,
      "deleted": false,
    },
    {
      "id": "3",
      "text": "Food shopping",
      "day": "March 7th at 2:00pm",
      "reminder": false,
      "editing": false,
      "completed": false,
      "deleted": false
    },

      ],
      showAddTask: false
    }
  },
  
  computed: {
    
    remaining() {
      return this.tasks.filter(task => !task.deleted).length
    },
    anyRemaining () {
      return this.remaining != 0
      
    },
    
    showClearCompletedButton() {
      return this.tasks.filter(task => task.completed).length > 0
    },
    
  },
  
  methods:{
    check (data){
      this.task.completed = data
      
    },
    
    
    finishedEdit (data) {

      const index = this.tasks.findIndex((task) => task.id == data.id)
      this.tasks.splice(index,1,data)
      
      
    },


    checkAllTasks (event) {
      this.tasks.forEach((task) => task.completed = event.target.checked)
    },
    clearCompleted() {
      
      this.tasks = this.tasks.map((task) => task.completed == true ? 
      {...task, deleted: !task.deleted } : task )
    },


    toggleAddTask() {
      this.showAddTask = !this.showAddTask
    },
    async addTask(task){
      const res = await fetch('api/tasks', {
        method: 'POST',
        headers: {
          'Content-type': 'application/json',
          }, 
          body: JSON.stringify(task),
      })

      const data = await res.json()

      this.tasks = [...this.tasks, data]
    },
    
    async deleteTask (id) {
       if (confirm('Are you sure?')) {
        const res = await fetch(`api/tasks/${id}`, {
          method: 'DELETE',
        })
        res.status === 200
        ? ( this.tasks = this.tasks.map((task) => task.id === id ? 
      {...task, deleted: !task.deleted } : task )) : alert('Error deleting task')
       }
      
       
      
    },
    /*
    editTask(id){
      alert('alive')
      this.editing = !this.editing
    },*/
    async toggleReminder(id){
        const taskToToggle = await this.fetchTask(id)
        const updTask = {...taskToToggle, reminder: !taskToToggle.reminder}

        const res = await fetch(`api/tasks/${id}`, {
            method: 'PUT' ,
            headers: {
                'Content-type': 'application/json'
            },
            body: JSON.stringify(updTask)
        })

        const data = await res.json()

      this.tasks = this.tasks.map((task) => task.id === id ? 
      {...task, reminder: data.reminder } : task )
    },
    async fetchTasks() {
      const res = await fetch('api/tasks')
      const data = await res.json()
      return data
    },
    async fetchTask(id) {
      const res = await fetch(`api/tasks/${id}`)
      const data = await res.json()
      return data
    },
  },
  async created(){
    this.tasks = await this.fetchTasks()
  },
  
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: 'Poppins', sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
.completed {
  text-decoration: line-through;
  color: grey;
}
.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
   margin-bottom: 14px;
}
.active {
  background: lightgreen;
}
 button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    margin: 3px;
    padding: 2px;
 }
  button:hover {
      background: lightgreen;
    }
  button:focus {
      outline: none;
    }
  
  

</style>