<template>
  <div>

    <label>Due date
      <input  style="width:11rem;" type="date" v-model="newTask.dueDate">
    </label>

    <label>Title
      <input  style="width:11rem;" type="text" v-model.trim="newTask.title" @keyup.enter="addTask">
    </label>

    <label>Description
      <input style="width:11rem;" type="text" v-model.trim="newTask.description" @keyup.enter="addTask">
    </label>

    <select style="background-color: #E74C3C; color: white;
     border-radius: 5%; border: 1px solid #2c3e50;"
      v-model="newTask.priority">
      <option value="high">high</option>
      <option value="medium">medium</option>
      <option value="low">low</option>
    </select>

    <button
      style="background-color: #E74C3C; color: white; border-radius: 5%; border: 1px solid #2c3e50; margin-top:1rem;"
      @click="addTask"
    >Add Task</button>

  </div>
</template>

<script>
export default {
  data () {
    return {
      baseURL: 'https://vue-todos.robertmckenney.ca/api',
      newTask: {}

    }
  },

  created () {
    this.newTask = this.getNewTask()
  },

  methods: {
    addTask () {
      // this.taskList.push(this.newTask)
      this.$emit('taskAdded', this.newTask)
      this.newTask = this.getNewTask()
    },

    getNewTask () {
      const now = new Date()
      const today = now.toISOString().substr([0], now.toISOString().indexOf('T'))
      console.log(now)
      return {
        id: (new Date()).getMilliseconds(),
        category: '',
        dueDate: today,
        isComplete: false,
        priority: 'medium',
        title: '',
        description: ''
      }
    }
  }
}
</script>

<style>

</style>
