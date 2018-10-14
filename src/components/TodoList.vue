<template>
  <div id="container">
    <input type="text" class="todo-input" placeholder="Add your todo here"
           v-model="newTodo" @keyup.enter="addNewTodo"/>
    <div v-for="(todo, index) in filteredTodoList" :key="index" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed" v-on:click="handleComplete(todo)"/>
        <div class="todo-item-label" v-if="!todo.editing" v-on:dblclick="editTodo(todo)"
        :class="{ completed: todo.completed }">
          {{todo.title}}
        </div>
        <input class="todo-item-edit" v-model="todo.title" type="text" v-else
        v-on:blur="finishEdit(todo)" @keyup.enter="finishEdit(todo)" v-focus/>
      </div>
      <div class="todo-remove" v-on:click="removeTodo(todo.id)"> &times; </div>
    </div>
    <div class="extra-container">
      <div>
        <input type="checkbox" v-model="checkAll" v-on:click="checkAllTodo"/>
        <label>Check all</label>
      </div>
      <div>{{remainingItem}} items left</div>
    </div>
    <div class="extra-container">
      <div>
        <button :class="{ active: this.filter === 'all' }" v-on:click="changeFilter('all')">
          All
        </button>
        <button :class="{ active: this.filter === 'active' }" v-on:click="changeFilter('active')">
          Active
        </button>
        <button :class="{ active: this.filter === 'completed' }" v-on:click="changeFilter('completed')">
          Completed
        </button>
      </div>
      <div>
        <button v-if="this.clearButton" v-on:click="clearCompleted">Clear completed</button>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
const BASE_URL = 'http://localhost:3000'
export default {
  name: 'TodoList',
  data () {
    return {
      newTodo: '',
      originalEditTitle: '',
      checkAll: false,
      filter: 'all',
      todoList: []
    }
  },

  methods: {
    getTodoList () {
      axios.get(BASE_URL + '/todoList').then(response => {
        this.todoList = response.data
      })
    },
    addNewTodo () {
      if (this.newTodo) {
        axios.post(BASE_URL + '/todoList', {
          title: this.newTodo,
          completed: false,
          editing: false
        }).then(
          this.getTodoList
        ).catch(error => {
          console.log(error)
        })
        this.newTodo = ''
      }
    },
    removeTodo (id) {
      axios.delete(BASE_URL + '/todoList/' + id)
        .then(
          this.getTodoList
        )
        .catch(error => {
          console.log(error)
        })
    },
    editTodo (todo) {
      if (!todo.completed) {
        this.originalEditTitle = todo.title
        todo.editing = true
      }
    },
    finishEdit (todo) {
      todo.editing = false
      if (!todo.title) {
        todo.title = this.originalEditTitle
      } else {
        axios.put(BASE_URL + '/todoList/' + todo.id, {
          title: todo.title,
          completed: todo.completed,
          editing: todo.editing
        })
          .then(
            this.getTodoList
          )
          .catch(error => {
            console.log(error)
          })
      }
    },
    handleComplete (todo) {
      axios.put(BASE_URL + '/todoList/' + todo.id, {
        title: todo.title,
        completed: !todo.completed,
        editing: todo.editing
      })
        .then(
          this.getTodoList
        )
        .catch(error => {
          console.log(error)
        })
    },
    checkAllTodo () {
      this.todoList.map(todo => {
        axios.put(BASE_URL + '/todoList/' + todo.id, {
          title: todo.title,
          completed: !this.checkAll,
          editing: todo.editing
        })
          .then(
            this.getTodoList
          )
          .catch(error => {
            console.log(error)
          })
      })
    },
    changeFilter (filter) {
      this.filter = filter
    },
    clearCompleted () {
      this.todoList.map(todo => {
        if (todo.completed) {
          this.removeTodo(todo.id)
        }
      })
    }
  },

  directives: {
    focus: {
      inserted: function (element) {
        element.focus()
      }
    }
  },
  mounted () {
    this.getTodoList()
  },
  computed: {
    remainingItem () {
      return this.todoList.filter(todo => !todo.completed).length
    },
    filteredTodoList () {
      // this.getTodoList()
      switch (this.filter) {
        case 'active':
          return this.todoList.filter(todo => todo.completed === false)
        case 'completed':
          return this.todoList.filter(todo => todo.completed === true)
        default:
          return this.todoList
      }
    },
    clearButton () {
      return this.todoList.filter(todo => todo.completed === true).length > 0
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .todo-input {
    width: 100%;
    font-size: 20px;
    margin-bottom: 16px;
    padding: 10px 5px;
  }
  .todo-item {
    font-size: 18px;
    display: flex;
    margin-bottom: 16px;
    justify-content: space-between;
  }
  .todo-item-left {
    display: flex;
    align-items: center;
  }
  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }
  .todo-item-edit {
    font-size: 18px;
    margin-left: 12px;
    padding: 10px;
    width: 100%;
    border: 1px solid #7dc7cc;
  }
  .todo-item-edit:focus {
    outline: none;
  }
  .todo-remove{
    cursor: pointer;
    margin-left: 14px;
  }
  .completed {
    text-decoration: line-through;
    color: grey;
  }
  .extra-container {
    padding-top: 16px;
    border-top: solid 1px grey;
    display: flex;
    justify-content: space-between;
  }
  .active {
    background-color: cornflowerblue;
  }
</style>
