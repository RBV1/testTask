<template lang="html">
  <div>
    <div class="uk-container uk-margin-top">
      <div class="uk-flex uk-flex-wrap uk-flex-between uk-flex-middle">
        <h1>Task List</h1>
        <button type="button" uk-toggle="#modalCreateTask" class="uk-button uk-button-primary">Create task</button>
      </div>
      <div class="uk-flex uk-flex-middle uk-margin">
          <p class="uk-margin-remove">Sort by:</p>
          <button @click="sortBy = 'name'" class="uk-button uk-button-text uk-text-medium uk-margin-left">Name</button>
          <button @click="sortBy = 'date'" class="uk-button uk-button-text uk-text-medium uk-margin-left">Date</button>
      </div>
      <div uk-grid class="uk-child-width-1-2@s uk-child-width-1-3@m uk-child-width-1-4@m">
        <div v-for="(task, index) in sortedTasks"  :key="index">
          <Task :task="task" v-on:deleteTask="showDeleteWarning"></Task>
        </div>
      </div>
    </div>
      <div id="modalCreateTask" uk-modal>
        <div class="uk-modal-dialog uk-modal-body">
            <button class="uk-modal-close-outside" type="button" uk-close></button>
            <h2 class="uk-modal-title">Create Task</h2>
            <form class="uk-form-stacked">
                <div class="uk-margin">
                    <label class="uk-form-label">Title</label>
                    <div class="uk-form-controls">
                      <input v-model="newTask.title" class="uk-input" type="text" placeholder="Title">
                    </div>
                </div>
                <div class="uk-margin">
                    <div class="uk-form-label">Description</div>
                    <div class="uk-form-controls">
                      <textarea v-model="newTask.description" class="uk-textarea" rows="5" placeholder="Description"></textarea>
                    </div>
                </div>
                <div class="uk-margin">
                  <label class="uk-form-label" for="form-stacked-select">Priority</label>
                  <div class="uk-form-controls">
                      <select v-model="newTask.priority" class="uk-select" id="form-stacked-select">
                          <option>1</option>
                          <option>2</option>
                          <option>3</option>
                          <option>4</option>
                      </select>
                  </div>
                </div>
                <div class="uk-margin">
                  <label class="uk-form-label" for="form-stacked-select">Category</label>
                  <div class="uk-form-controls">
                      <select v-model="newTask.category" class="uk-select" id="form-stacked-select">
                          <option>Work</option>
                          <option>Home</option>
                          <option>Study</option>
                      </select>
                  </div>
                </div>
                <div class="uk-margin">
                  <label class="uk-form-label" for="form-stacked-select">Date</label>
                  <div class="uk-form-controls">
                    <Datepicker v-model="newTask.date" format="MMMM d, yyyy" input-class="uk-input"></Datepicker>
                  </div>
                </div>
            </form>
            <div v-if='showModalError' class="uk-alert-danger" uk-alert>
                <a class="uk-alert-close" uk-close></a>
                <p>Please, fill all required fields !</p>
            </div>
            <button class="uk-button uk-button-primary" @click="createTask()" type="button">Save</button>
            <button class="uk-modal-close uk-button uk-button-secondary" type="button">Back</button>
        </div>
    </div>
    <div id="deleteWarning" uk-modal>
    <div class="uk-modal-dialog">
        <div class="uk-modal-header">
            <h2 class="uk-modal-title">Delete</h2>
        </div>
        <div class="uk-modal-body">
          <p>Are you sure want delete this task ?</p>
        </div>
        <div class="uk-modal-footer">
          <button @click="deleteTask" class="uk-button uk-button-danger uk-modal-close">Yes, delete</button>
          <button class="uk-button uk-button-default uk-modal-close">No</button>
        </div>
    </div>
</div>
  </div>
</template>

<script>
import Datepicker from 'vuejs-datepicker'
import Task from './Task'
import UIkit from 'uikit'

const STORAGE_KEY = 'taskList'
const taskStorage = {
  fetch () {
    let tasks = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    return tasks
  },
  save (tasks) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(tasks))
  }
}

let sorting = {
  name (tasks) {
    return tasks.sort((taskA, taskB) => {
      if (taskA.title < taskB.title) {
        return -1
      } else if (taskA.title > taskB.title) {
        return 1
      }
      return 0
    })
  },
  date (tasks) {
    return tasks.sort((taskA, taskB) => {
      let dateTaskA = new Date(taskA.date)
      let dateTaskB = new Date(taskB.date)

      if (dateTaskA < dateTaskB) {
        return -1
      } else if (dateTaskA > dateTaskB) {
        return 1
      }
      return 0
    })
  }
}

export default {
  data () {
    return {
      tasks: taskStorage.fetch(),
      sortBy: 'name',
      taskForDelete: undefined,
      newTask: {
        title: '',
        description: '',
        date: '',
        priority: '',
        category: ''
      },
      showModalError: false
    }
  },

  computed: {
    sortedTasks () {
      return sorting[this.sortBy](this.tasks)
    }
  },

  // watch tasks change for localStorage persistence
  watch: {
    tasks: {
      handler (tasks) {
        taskStorage.save(tasks)
      },
      deep: true
    }
  },

  methods: {
    createTask () {
      let fieldOfNewTask = Object.keys(this.newTask)
      let newTask = this.newTask
      let isInvalid = fieldOfNewTask.some(field => {
        return newTask[field].toString().trim().length === 0
      })

      if (isInvalid) {
        this.showModalError = true
        return
      }

      UIkit.modal('#modalCreateTask').hide()

      this.tasks.push(Object.assign({}, this.newTask))
      this.showModalError = false
      for (let field of fieldOfNewTask) {
        this.newTask[field] = ''
      }
    },

    showDeleteWarning (task) {
      this.taskForDelete = task
      UIkit.modal('#deleteWarning').show()
    },

    deleteTask () {
      this.tasks.splice(this.tasks.indexOf(this.taskForDelete), 1)
    }
  },

  components: {
    Datepicker,
    Task
  }
}
</script>

<style lang="less">
</style>
