<template>
  <div>
    <h2 style="color: #2c3e50; font-size:20px;">{{listTitle}}</h2>
    <div class="toolbar">
      <label style="font-size:10px;" for="priority-filter">Show Priority</label>
      <select style="background-color: #E74C3C; color: white; border-radius: 5%;
       border: 1px solid #2c3e50;"
       name="priority-filter" id="priority-filter" v-model="selectedPriority">
        <option value="">All</option>
        <option
          v-for="option in priorityOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>

    <div style="margin-top:-35px; margin-left:20rem;" class="toolbar">
      <label style="font-size:10px; margin-left: -2rem;"
       for="category-filter">Show Category</label>
      <select style="background-color: #E74C3C; color: white; border-radius: 5%;
       border: 1px solid #2c3e50;"
       name="category-filter" id="category-filter" v-model="selectedCategory">
        <option value="">All</option>
        <option
          v-for="option in categoryOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>

<div style="margin-top:-35px; margin-left:43.8rem;"
 class="toolbar">
      <label style="font-size:10px;"
      for="dueDate-filter">Sort By</label>
      <select
        style="background-color: #E74C3C; color: white; border-radius: 5%; border: 1px solid #2c3e50;"
        name="dueDate-filter"
        id="dueDate-filter"
        v-model="sortAscending">
        <option value="">All</option>
        <option
          v-for="option in dueDateOptions"
          :value="option.value"
          :key="option.label">{{option.label}}</option>
      </select>
    </div>

    <ul class="task-list">
      <li v-for="task in sortedTasks" :key="task.id" class="task-item">
       <span v-if="(task.id === isEditing)">
         <label>Title</label>
         <input type="text" v-model="editingTask.title">
         <label>Description</label>
         <input type="text" v-model="editingTask.description">

          <div class="toolbar">
      <label for="category-filter"></label>
      <select
       style="background-color: #E74C3C; color: white;
        border-radius: 5%; border: 1px solid #2c3e50;
         margin-top: 2rem; margin-bottom: 2rem;
          margin-left: -0.5rem; width: 10rem;"
       name="category-filter" id="category-filter"
        v-model="editingTask.category">
        <option value="">Category</option>
        <option
          v-for="option in categoryOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>

<div class="toolbar">
      <label for="priority-filter"></label>
      <select style="background-color: #E74C3C;
       color: white; border-radius: 5%; border: 1px solid #2c3e50;
        margin-top: 0.5rem; margin-bottom: 2rem; margin-left: -0.5rem;
         width: 10rem;"
          name="priority-filter" id="priority-filter" v-model="editingTask.priority">
        <option
          v-for="option in priorityOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>

         <button
          style="background-color: #E74C3C;
           color: white; border-radius: 5%;
            border: 1px solid #2c3e50;" @click="saveEditing(task)">SAVE</button>
          <button
           style="background-color: #E74C3C;
            color: white; border-radius: 5%;
             border: 1px solid #2c3e50;" @click="cancelEditing">Cancel</button>
       </span>

       <span style="border: 1px solid #E74C3C;
       width:55rem; height:4rem; margin-top:0.5rem;
        padding-left:2rem; padding-top:1rem;" v-else>
        <span style="color: #E74C3C; margin-right: 3rem;"
         class="fa"
          :class="{
            'fa fa-circle-o': ! task.isComplete,
            'fa-check-circle': task.isComplete
          }"
          @click="toggleDone(task)"></span>

        <span style="cursor: pointer; padding-right:10rem;"
         @click="setIsEditing(task)">{{ task.title }}</span>
        <span  style="color: #E74C3C; margin-right: 3rem; font-size:10px;"
        >{{ task.priority }}</span>
        <span style="color: #979A9A; margin-right: 3rem;
          position: sticky; font-size:10px;">{{task.category}}</span>
        <span style="color: #979A9A; margin-right: 5rem;
          position: sticky; font-size:10px;">{{task.dueDate}}</span>
        <span style="color: #E74C3C; margin-right: 1rem;
         position: sticky; position: -webkit-sticky; top: 0;"
          class="fa fa-trash" @click="removeTask(task)"></span>
        </span>
      </li>
    </ul>
  </div>
</template>

<script>
/* eslint-disable */
import axios from 'axios'


export default {
  // We are not actually managing the task list in this component.
  // We are only displaying the list. The parent component (App.vue) is
  // passing down a reference to the list in the 'tasks' prop.
  props: ['tasks', 'listTitle'],
  data () {
    return {
      baseURL: 'https://vue-todos.robertmckenney.ca/api',
      priorityOptions: ['low', 'medium', 'high'],
     selectedPriority: '',
     categoryOptions: ['Graphic Design', 'Motion Graphic', 'Web development'],
     selectedCategory: '',
      dueDateOptions: [{value: true, label: 'Earliest'}, {value: false, label: 'Latest'}],
      selectedDueDate: '',
      isEditing: 0,
      editingTask: {},
      sortAscending: true

    }
  },
  computed: {
    filteredPriorityTasks () {
      // If the selectedFilter is 'all', the value is set to an empty string
      // which will evaluate to false. In that case, we will reuturn the
      // entire task list. Otherwise we use the array.filter() method to return
      // only the tasks with the selected priority.
      return (!this.selectedPriority)
        ? this.tasks
        : this.tasks.filter(task => task.priority === this.selectedPriority)
    },
    filteredCategoryTasks () {
      return (!this.selectedCategory)
        ? this.tasks
        : this.tasks.filter(task => task.category === this.selectedCategory)
    },

    filteredTasks (){
      const merged = [...new Set(
        [...this.filteredPriorityTasks, ...this.filteredCategoryTasks]
      )]

      return merged.filter(task =>
       this.filteredPriorityTasks.includes(task) &&
       this.filteredCategoryTasks.includes(task)
       )
    },
    // filteredTasks () {
    //   return (!this.selectedPriority && !this.selectedCategory && !this.selectedDueDate)
    //     ? this.tasks
    //     : this.tasks.filter(task => {
    //       let matched = false
    //       if (this.selectedPriority && this.selectedCategory && this.selectedDueDate) {
    //         if (task.priority === this.selectedPriority &&
    //             task.category === this.selectedCategory &&
    //             task.dueDate === this.selectedDueDate) {
    //           matched = true
    //         }
    //       } else if (this.selectedPriority) {
    //         if (task.priority === this.selectedPriority) matched = true
    //       } else if (this.selectedCategory) {
    //         if (task.category === this.selectedCategory) matched = true
    //        } else if (this.selectedDueDate) {
    //         if (task.dueDate === this.selectedDueDate) matched = true
    //       }
    //       return matched
    //     })
    // },
    sortedTasks () {
      let allTasks = [...this.filteredTasks]
      return allTasks.sort((a, b) => {
        let date1 = new Date(a.dueDate)
        let date2 = new Date(b.dueDate)
        return this.sortAscending ? (date1 - date2) : (date2 - date1)
      })
    }
  },
  methods: {
    // Since this component does not own the task list data, we need to
    // notify the parent component that the user wants to toggle the
    // completion status of the task.  We do that by emitting a custom event,
    // and passing up the affected task with the event. We will listen for
    // this event in the parent component and call the appropriate method to
    // make the change to the data.
    toggleDone (task) {
      this.$emit('toggleDone', task)
    },
    removeTask (task) {
      this.$emit('removeTask', task)
    },
    setIsEditing (task) {
      this.editingTask = {...task}
      this.isEditing = task.id
    },
    cancelEditing () {
     this.editingTask = {}
     this.isEditing = 0
    },
    saveEditing (task) {
      task = Object.assign(task, {...this.editingTask})
      this.editingTask = {}
      this.isEditing = 0
    },


    created () {

    axios.get(`${this.baseURL}/priorities`)
      .then(response => {
        this.priorityOptions = response.data.data.length
          ? response.data.data
          : []
      })
      .catch(error => { console.error(error) })


      axios.get(`${this.baseURL}/priorities/{Id}`)
      .then(response => {
        this.priorityOptions = response.data.data.length
          ? response.data.data
          : []
      })
      .catch(error => { console.error(error) })





       axios.get(`${this.baseURL}/categories`)
      .then(response => {
        this.categoryOptions = response.data.data.length
          ? response.data.data
          : []
      })
      .catch(error => { console.error(error) })


        axios.get(`${this.baseURL}/categories/{Id}`)
      .then(response => {
        this.categoryOptions = response.data.data.length
          ? response.data.data
          : []
      })
      .catch(error => { console.error(error) })




       axios.get(`${this.baseURL}/dueDates`)
      .then(response => {
        this.dueDateOptions = response.data.data.length
          ? response.data.data
          : []
      })
      .catch(error => { console.error(error) })


        axios.get(`${this.baseURL}/dueDates/{Id}`)
      .then(response => {
        this.dueDateOptions = response.data.data.length
          ? response.data.data
          : []
      })
      .catch(error => { console.error(error) })

    }
  }
}
</script>

<style>
/* We will add styles here later. */
.task-item {
  display: grid;
  grid-template-columns: auto 1fr auto auto;
  grid-gap: 1rem;
  align-items: center;
}
.toolbar {
  margin-bottom: 1rem;
}

label {
  font-size: 10px;
}

span:hover{
  background-color:rgb(243, 240, 240);
  transition: 0.4s;
}

</style>
