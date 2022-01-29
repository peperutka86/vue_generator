<template>
<div class="container">
  <Header  @toggle-add-task ="toggleAddTask"  title="Task Tracker" :showAddTask="showAddTask" />
  <div v-show="showAddTask">
    <AddTask  @add-task="addTask" />
  </div>
  
  <Tasks @toggle-reminder="toggleReminder"  @delete-task="deleteTask" :tasks="tasks" />
</div>
</template>

<script>


import Header from './components/Header.vue'
import Tasks from './components/Tasks.vue'
import AddTask from './components/AddTask.vue'


export default {
  name: 'App',
  components: {
    Header,
    Tasks,
    AddTask
   
    
  },
  data() {
    return {
      tasks: [],
      showAddTask: false
    }
  },
  methods: {
    toggleAddTask(){
      this.showAddTask = !this.showAddTask
    },
    async addTask(task){
    const res = await fetch('http://localhost:3000/tasks', 
    {
      method: 'POST',
      headers: {
        'Content-type': 'application/json',
      },
      body: JSON.stringify(task),
    })
     
      const data = await res.json()
      this.tasks = [... this.tasks, data]
    },
    async deleteTask(id){
      if (confirm('Are you sure?')) {
        const res = await fetch(`http://localhost:3000/tasks/${id}`,{
          method: 'DELETE',
        }
        )
        res.status === 200
        ? (this.tasks = this.tasks.filter((task) => task.id !== id))
        : alert('Error deleting task')


      
      }
    },
    async toggleReminder(id){
      const taskToToggle =  await this.fetchTask(id)
      const updTask = 
      {...taskToToggle, reminder: !taskToToggle.reminder}

      const res = await fetch(`http://localhost:3000/tasks/${id}`, {
        method: 'PUT',
        headers: {
          'Content-type': 'application/json'
        },
        body: JSON.stringify(updTask),
      })
      const data = await res.json()

      this.tasks = this.tasks.map((task) => task.id === id ? {...task, reminder: data.reminder} : task)
    },
     async fetchTasks() {
      const res = await fetch('http://localhost:3000/tasks')
      const data = await res.json()
      return data
    },
    
   
    async fetchTask(id) {
      const res = await fetch(`http://localhost:3000/tasks/${id}`)
      const data = await res.json()
      return data
    }
    },
      
   async created(){
    this.tasks=  await this.fetchTasks()
  //  [
     
  //   {
  //     id: 1,
  //     text: 'Doctors Appointment',
  //     day: 'March 5th at 2:30pm',
  //     reminder: true
  //   },
  //   {
  //     id: 2,
  //     text: 'Meeting with boss',
  //     day: 'March 6th at 1:30pm',
  //     reminder: true
  //   },
  //   {
  //     id: 3,
  //     text: 'Food shopping',
  //     day: 'March 7th at 2:00pm',
  //     reminder: false
  //   },
  // ]

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

</style>
