<template>
  <div id="container">
    <input type="text" class="todo-input" placeholder="Add your todo here"
           v-model="newTodo" @keyup.enter="addNewTodo"/>
    <div v-for="(todo, index) in todoList" :key="todo.id" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed" v-on:click="handleComplete(todo)"/>
        <div class="todo-item-label" v-if="!todo.editing" v-on:dblclick="editTodo(todo)"
        :class="{ completed: todo.completed }">
          {{todo.title}}
        </div>
        <input class="todo-item-edit" v-model="todo.title" type="text" v-else
        v-on:blur="finishEdit(todo)" @keyup.enter="finishEdit(todo)" v-focus/>
      </div>
      <div class="todo-remove" v-on:click="removeTodo(index)"> &times; </div>
    </div>
    <div class="extra-container">
      <div>
        <input type="checkbox" v-model="checkAll" v-on:click="checkAllTodo"/>
        <label>Check all</label>
      </div>
      <div>{{remainingItem}} items left</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      originalEditTitle: '',
      checkAll: false,
      todoList: [
        {
          'id': 1,
          'title': 'Learn Vue js',
          'completed': false,
          'editing': false
        },
        {
          'id': 2,
          'title': 'Vuetify also',
          'completed': false,
          'editing': false
        }
      ]
    }
  },

  methods: {
    addNewTodo () {
      if (this.newTodo) {
        this.todoList.push({
          id: this.idForTodo,
          title: this.newTodo,
          completed: false,
          editing: false
        })

        this.idForTodo++
        this.newTodo = ''
      }
    },
    removeTodo (index) {
      this.todoList.splice(index, 1)
    },
    editTodo (todo) {
      if (!todo.completed) {
        this.originalEditTitle = todo.title
        todo.editing = true
      }
    },
    finishEdit (todo) {
      if (!todo.title) {
        todo.title = this.originalEditTitle
      }
      todo.editing = false
    },
    handleComplete (todo) {
      todo.completed = !todo.completed
    },
    checkAllTodo () {
      this.todoList.map(todo => {
        todo.completed = !this.checkAll
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

  computed: {
    remainingItem () {
      return this.todoList.filter(todo => !todo.completed).length
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
</style>
