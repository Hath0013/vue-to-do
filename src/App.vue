<template>
  <div id="app">
    <div v-if="isLoggedIn">

      <!-- We need some navigation links to switch between views -->
      <nav>
        <ul>
          <!--
            The RouterLink component comes with VueRouter.
            The 'to' prop is the URL route.
          -->
          <li><router-link to="/" exact>All</router-link></li>
          <li><router-link to="/active">Active</router-link></li>
          <li><router-link to="/completed">Completed</router-link></li>
        </ul>
      </nav>
      <h1 class="app-title">Vue Todos</h1>
      <add-task-form @taskAdded="addTask" />
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
    <login-page v-else @saveApiTokens="saveApiTokens" />
  </div>
</template>

<script>
import axios from 'axios'
import moment from 'moment'
import AddTaskForm from '@/components/AddTaskForm'
import LoginPage from '@/pages/LoginPage'


export default {
  name: 'App',
  components: { LoginPage, AddTaskForm },
  data () {
    return {
      taskList: [
        // { id: 1234, title: 'Learn Vue', isComplete: true, priority: 'med' },
        // { id: 1235, title: 'Learn Vue Router', isComplete: false, priority: 'med' },
        // { id: 1236, title: 'Learn Vuex', isComplete: false, priority: 'med' },
        // { id: 1237, title: 'Learn Vue DevTools', isComplete: true, priority: 'med' }
      ],
      baseURL: 'https://vue-todos.robertmckenney.ca/api'
    }
  },

  created () {
    this.taskList = JSON.parse(localStorage.getItem('taskList')) || []
  },

  methods: {
    addTask (task) {
      axios.post(`${this.baseURL}/todos`, task)
        .then(response => {
          this.taskList.push(response.data.data)
        })
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

nav {
  display: inline-block;
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

.app-title {
  display: inline-block;
  text-align: right;
  font-weight: normal;
}

a.router-link-active {
  color: #01cdd7;
  border-top: solid 3px #9c3f10;
  padding-top: .3rem;
}

.li.router-link-active a {
  color: #01cdd7;
  padding-top: .3rem;
}

a {
  text-decoration: none;
  color: #424242;
  font-weight: bold;
  font-size: 1.5rem;
}

@media only screen and (min-width: 38rem) {

  .app-title {
    margin-left: 7rem;
  }

}

</style>
