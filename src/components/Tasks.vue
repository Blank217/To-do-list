<template>
    <div :key="task.id" v-for="task in tasksFiltered" >

        <Task @toggle-reminder="$emit('toggle-reminder',task.id)" 
        @finishedEdit="emitdouble"
        
        
        @delete-task="$emit('delete-task',task.id,this.deleted = true)" :task='task'
        />
    </div>
        
</template>

<script>
import Task from './Task'

export default {
    name: 'Tasks',
    props: {
        tasks: Array,
        task: Object,
        checkAll: Boolean,
        filter: String
    
        
    },
    components:{
        Task
    },
    
    computed: {
        tasksFiltered() {
      if (this.filter == 'all') {
        return this.tasks.filter(task => !task.deleted)
      } else if (this.filter == 'active') {
        return this.tasks.filter(task => !task.completed)
      } else if (this.filter == 'completed') {
        return this.tasks.filter(task => task.completed)
      }
        else if (this.filter =='showalldeleted'){
        return this.tasks.filter(task => task.deleted)
        }
      return this.tasks
    },
    },
   
    methods: {
        emitdouble (data){
            this.$emit('finishedEdit', data
        )
        },
        
    },
    

    
    
    emits: ['delete-task','toggle-reminder','finishedEdit'],

    
 }
</script>