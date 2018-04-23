<template>
  <div>
    <h2>{{listTitle}}</h2>
    <h4>Sort by:</h4>
    <div class="toolbar block margin-r">
      <label for="priority-filter" class="pad">Priority</label>
      <select name="priority-filter" id="priority-filter" v-model="selectedPriority">
        <option value="">all</option>
        <option
          v-for="option in priorityOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>
    <div class="toolbar block margin-r">
      <label for="category-filter" class="pad">Category</label>
      <select name="category-filter" id="category-filter" v-model="selectedCategory">
        <option value="">all</option>
        <option
          v-for="option in categoryOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>
    <!-- add task date dropdown -->
    <div class="toolbar block margin-r">
      <button @click="sortAscending = !sortAscending">Sort</button>
    </div>
    <!-- end task date dropdown -->
    <ul class="task-list-headings">
      <li>Task</li>
      <li>Priority</li>
      <li>Category</li>
      <li>Due</li>
    </ul>
    <ul class="task-list">
      <li v-for="task in filteredTasks" :key="task.id" class="task-item">
        <span class="far"
          :class="{
            'fa-circle': ! task.isComplete,
            'fa-check-circle': task.isComplete
          }"
          @click="toggleDone(task)"></span>
        <span class="title">{{ task.title }}</span>
        <span :class="{
          'low': task.priority === 'low',
          'med': task.priority === 'med',
          'high': task.priority === 'high'
          }"
        >{{ task.priority }}</span>
        <span>{{ task.category }}</span>
        <span>{{ task.dueDate }}</span>
        <span class="far fa-trash-alt" @click="removeTask(task)"></span>
      </li>
    </ul>
  </div>
  <button @click="logout">LOGOUT</button>
</template>

<script>
export default {
  // We are not actually managing the task list in this component.
  // We are only displaying the list. The parent component (App.vue) is
  // passing down a reference to the list in the 'tasks' prop.
  props: ['tasks', 'listTitle'],
  data () {
    return {
      priorityOptions: ['low', 'med', 'high'],
      selectedPriority: '',
      categoryOptions: ['school', 'home', 'work'],
      selectedCategory: '',
      sortAscending: true
    },
  created () {
    this.loadInitialData()
  },
  computed: {
    axiosOptions () {
      return {
        baseURL: this.baseURL,
        headers: { 'Authorization': `Bearer ${this.api.accessToken}` }
      }
    },
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
    filteredTasks () {
      return (!this.selectedPriority && !this.selectedCategory)
        ? this.tasks
        : this.tasks.filter(task => {
          let matched = false
          if (this.selectedPriority && this.selectedCategory) {
            if (task.priority === this.selectedPriority &&
                task.category === this.selectedCategory) {
              matched = true
            }
          } else if (this.selectedPriority) {
            if (task.priority === this.selectedPriority) matched = true
          } else if (this.selectedCategory) {
            if (task.category === this.selectedCategory) matched = true
          }
          return matched
        })
    },
    // add task date functionality
    sortedTasks () {
      let tempList = [...this.filteredTasks]
      return tempList.sort((a, b) => {
        const dateOne = new Date(a.dueDate)
        const dateTwo = new Date(b.dueDate)
        return this.sortAscending ? (dateOne - dateTwo) : (dateTwo - dateOne)
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
    refreshTasks () {
      axios
        .get('/todos', this.axiosOptions)
        .then(response => { this.taskList = response.data.data })
        .catch(error => { this.handleError(error) })
    },
    addTask (task) {
      axios
        .post('/todos', task, this.axiosOptions)
        .then(response => { this.taskList.push(response.data.data) })
        .catch(error => { this.handleError(error) })
    },
    toggleDone (task) {
      task.isComplete = !task.isComplete
      axios
        .put(`/todos/${task.id}`, task, this.axiosOptions)
        .catch(error => { this.handleError(error) })
    },
    removeTask (task) {
      axios
        .delete(`/todos/${task.id}`, this.axiosOptions)
        .then(response => {
          const taskIndex = this.taskList.findIndex(t => t.id === task.id)
          this.taskList.splice(taskIndex, 1)
        })
        .catch(error => { this.handleError(error) })
    },
    handleError(error) {
      console.log(error)
      // obviously we want to do something more robust
      // including notifying the user somehow.
    },
    loadInitialData () {
      const apiTokens = JSON.parse(localStorage.getItem('todoApiTokens'))
      if (apiTokens) {
        this.api.accessToken = apiTokens.access_token
        this.api.expiresAt = apiTokens.expires_at
        if (this.isLoggedIn) this.refreshTasks()
      }
    }
  }
}
</script>

<style>

.task-item {
  display: grid;
  grid-template-columns: auto 1fr 1fr 1fr 1fr auto;
  align-items: center;
  font-size: .6rem;
}

.task-list-headings {
  font-weight: bold;
  font-size: .6rem;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  align-items: center;
}

.fa-circle, .fa-check-circle {
  padding-right: .5rem;
}

.toolbar {
  margin-bottom: 1rem;
}
.fa-trash-alt {
  color: red;
}

.low {
  color: green;
}

.med {
  color: orange;
}

.high {
  color: red;
}

.title {
  font-weight: bold;
  text-align: left;
}

.margin-r {
  margin-right: 2rem;
}

@media only screen and (min-width: 38rem) {

  .task-list-headings {
    font-weight: bold;
    font-size: 1rem;
  }

  .task-item {
    font-size: .9rem;
  }

}

</style>
