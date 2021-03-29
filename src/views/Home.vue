<template>
    <div v-if="showAddTask" >
      <AddTaskForm @add-task="addNewTask" />
    </div>
    <Tasks @toggle-reminder="toggleTask" @delete-task="deleteTask" :tasks="tasks" />
  
</template>

<script>
import Tasks from '../components/Tasks'
import AddTaskForm from '../components/AddTaskForm'

export default {
       name: 'Home',
       props:{
                showAddTask: Boolean
       },
       components:{
            Tasks,
            AddTaskForm,
       },
       data(){
                return {
                  tasks: [],
                }
       
       },

       async created(){
         this.tasks = await this.fetchTasks()
       },

       methods: {
         async deleteTask(id){
             if(confirm('Are you sure ?')){
                  const res = await fetch(`api/tasks/${id}`, { method: "DELETE"})
                  res.status == 200 ?
                  this.tasks = this.tasks.filter(task => id !== task.id)
                  : alert('Error while deleting task')
             }
         },

         async toggleTask(id){
                  const getTaskToUpdate = await this.fetchTask(id)
                  const updateTask = await {...getTaskToUpdate, reminder: !getTaskToUpdate.reminder}

                  const res = await fetch(`api/tasks/${id}`,{
                  method: 'PUT',
                  headers:{
                           'Content-type': 'application/json'
                  },
                  body: JSON.stringify(updateTask)
                  })

                  const data = await res.json()

                  this.tasks = this.tasks.map(task => id === task.id ? {...task, reminder: data.reminder} : task)
         },

         async fetchTasks(){
                  const res = await fetch('api/tasks')
                  const data = await res.json()

                  return data
         },

         async fetchTask(id){
                  const res = await fetch(`api/tasks/${id}`)
                  const data = await res.json()

                  return data
         },

         async addNewTask(task){
                  const res = await fetch('api/tasks', {
                           method: 'POST',
                           headers:{
                           'Content-type': 'application/json',
                           },
                           body:JSON.stringify(task),
                  })

                  const data = await res.json()

                  this.tasks = [...this.tasks, task]
         },
       },
}
</script>

<style >

</style>