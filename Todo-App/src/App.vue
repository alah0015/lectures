<template>
  <div id="app">
    <!-- We need some navigation links to switch between views -->
  <nav style="background-color: #E74C3C; border: 1px solid #2c3e50;" class="navbar navbar-inverse">
    <div class="navbar-header">
      <a style="color: white;" class="navbar-brand" href="#">Vue To-Do</a>
    </div>
      <ul class="nav navbar-nav">
        <!--
          The RouterLink component comes with VueRouter.
          The 'to' prop is the URL route.
        -->
        <li style="color: #FDEDEC;"><router-link  to="/">All</router-link></li>
        <li  style="color: #FDEDEC;"><router-link to="/active">Active</router-link></li>
        <li style="color: #FDEDEC;"><router-link  to="/completed">Completed</router-link></li>
      </ul>
    </nav>

    <add-task-form @taskAdded="addTask"/>
    <!--
      The RouterView is a placeholder that VueRouter uses to know
      where to insert the designated component for a given URL.
      In this case we are passing our taskList as a prop to each route's
      component and we are listening for user events.  This way we are still
      only mainting one master list in the App.vue component.
     -->
    <router-view
      :tasks="taskList"
      @toggleDone="toggleDone"
      @removeTask="removeTask"
    />
  </div>
  <!-- this is a comment -->
</template>

<script>
/* eslint-disable */
import axios from 'axios'
import moment from 'moment'
import AddTaskForm from '@/components/AddTaskForm'

export default {
  name: 'App',
  components: { AddTaskForm },
  data () {
    return {
       baseURL: 'https://vue-todos.robertmckenney.ca/api',

      taskList: [
        { id: 1234, title: 'Learn Vue', isComplete: true, priority: 'medium' },
        { id: 1235, title: 'Learn Vue Router', isComplete: false, priority: 'medium' },
        { id: 1236, title: 'Learn Vuex', isComplete: false, priority: 'medium' },
        { id: 1237, title: 'Learn Vue DevTools', isComplete: true, priority: 'medium' }
      ]

    }
  },


  created () {
    this.taskList = JSON.parse(localStorage.getItem('taskList')) || []
  },

  methods: {
    addTask (task) {
      this.taskList.push(task)
      this.saveTaskList()
    },

    toggleDone (task) {
      task.isComplete = !task.isComplete
      this.saveTaskList()
    },

    removeTask (task) {
      const taskIndex = this.taskList.indexOf(task)
      this.taskList.splice(taskIndex, 1)
      this.saveTaskList()
    },

    saveTaskList () {
      localStorage.setItem('taskList', JSON.stringify(this.taskList))
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  width: 100%;
  max-width: 550px;
  margin: 60px auto;

}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
nav ul {
  display: flex;
  justify-content: flex-start;

}
nav ul li {
  margin-right: 1em;

}

</style>
