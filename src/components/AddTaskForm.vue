<template>
  <div>
    <label class="block">Title
      <input type="text" v-model.trim="newTask.title" @keyup.enter="addTask">
    </label>
    <label class="width block">Due
      <input type="date" v-model="newTask.dueDate">
    </label>
    <div class="inline pad block">
      <label class="pad">Priority</label>
      <select v-model="newTask.priority">
        <option value="high">high</option>
        <option value="med">med</option>
        <option value="low">low</option>
      </select>
    </div>
    <div class="inline pad block">
      <label>Category</label>
      <select v-model="newTask.category">
        <option value=""></option>
        <option value="school">school</option>
        <option value="work">work</option>
        <option value="home">home</option>
      </select>
    </div>
    <button @click="addTask">Add Task</button>
  </div>

</template>

<script>
export default {
  data () {
    return {
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
      const today = now.toISOString().substr(0, now.toISOString().indexOf('T'))
      console.log(now)
      return {
        id: null,
        category: 'home',
        dueDate: today,
        isComplete: false,
        priority: 'med',
        title: ''
      }
    }
  }
}

</script>

<style>

  .pad {
    padding-bottom: 1rem;
  }

  input,
  select {
    border: solid 2px #01cdd7;
    border-radius: 5px;
    font-size: .7rem;
    padding: .3rem;
  }

  input {
    margin-bottom: 1rem;
  }

  button {
    font-size: 1rem;
    font-weight: bold;
    color: #fff;
    background-color: #01cdd7;
    border: solid 2px #01cdd7;
    border-radius: 5px;
    padding: .2rem;
    width: 150px;
  }

  label {
      font-size: 1rem;
      padding-right: 1rem;
  }

  .inline {
    display: inline-block;
  }

  .block {
   display: block;
  }

@media only screen and (min-width: 38rem) {

  .block {
   display: inline-block;
  }

  input,
  select {
    border: solid 2px #01cdd7;
    border-radius: 5px;
    font-size: .7rem;
    padding: .3rem;
  }

  button {
    width: 250px;
  }
}

</style>
