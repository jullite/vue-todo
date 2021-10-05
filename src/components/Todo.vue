<template>
  <div class="container">
    <header><h1>Todos</h1></header>
    <section>
      <b-form class="add-form" @submit="addTodo" v-on:submit.prevent>
        <b-input
          size="lg"
          type="text"
          class="todo-input"
          v-model="newTodo"
          placeholder="what need to be done"
        />
        <b-button type="submit">add</b-button>
      </b-form>
      <todoItem
        v-for="todo in todosFilter"
        :key="todo.id"
        :todo="todo"
        :checkAllTodos="allCompleted"
        @removeTodo="removeTodo"
        @editTodo="editTodo"
      >
      </todoItem>
    </section>
    <footer>
      <label for="checkAll">
        <input
          type="checkbox"
          class="checkbox-round check-all"
          :checked="allCompleted"
          @change="checkAllTodos"
        />check all
      </label>
      <b-dropdown size="sm" :text='visibility' variant="outline-secondary">
        <b-dropdown-item @click="visibility = 'all'">all</b-dropdown-item>
        <b-dropdown-item @click="visibility = 'active'">active</b-dropdown-item>
        <b-dropdown-item @click="visibility = 'completed'"
          >completed</b-dropdown-item
        >
      </b-dropdown>
      <b-button
        v-if="showClearCompletedBtn"
        size="sm"
        variant="outline-secondary"
        @click="clearCompleted"
        >clear completed</b-button
      >
      <span>{{ leftTodos }} items left</span>
    </footer>
  </div>
</template>

<script>
import todoItem from "./TodoItem.vue";
var STORAGE_KEY = "vue-todos";
var todoStorage = {
  fetch: () => {
    var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
    todos.forEach((todo, index) => {
      todo.id = index;
    });
    todoStorage.uid = todos.length;
    return todos;
  },
  save: (todos) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
  },
};

var filter = {
  all(todos) {
    return todos;
  },
  active(todos) {
    return todos.filter((todo) => !todo.completed);
  },
  completed(todos) {
    return todos.filter((todo) => todo.completed);
  },
};

export default {
  name: "todo-list",
  components: {
    todoItem,
  },
  data() {
    return {
      todos: todoStorage.fetch(),
      newTodo: "",
      visibility: "all",
    };
  },
  watch: {
    todos: {
      handler: (todos) => {
        todoStorage.save(todos);
      },
    },
    deep: true,
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.unshift({
        id: todoStorage.uid++,
        title: this.newTodo,
        completed: false,
        editing: false,
      });
      this.newTodo = "";
    },
    removeTodo(todo) {
      this.todos.splice(this.todos.indexOf(todo), 1);
    },
    editTodo(editedTodo) {
      this.todos.forEach((todo, index) => {
        if (todo.id == editedTodo.id) {
          this.todos.splice(index, 1, editedTodo);
        }
      });
    },
    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
    checkAllTodos() {
      this.todos.forEach((todo) => (todo.completed = !todo.completed));
    },
  },
  computed: {
    leftTodos() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    allCompleted() {
      return Boolean(this.leftTodos == 0 & this.todos.length != 0);
    },
    todosFilter() {
      return filter[this.visibility](this.todos)
    },
    showClearCompletedBtn() {
      return this.todos.filter((todo) => todo.completed).length > 0;
    },
  },
};
</script>


<style>
.container {
  width: 600px;
  height: auto;
  margin: 0 auto;
  padding: 0px;
  border: 1px solid lightgrey;
}
.add-form {
  display: flex;
  margin-bottom: 5px;
}
.add-form button {
  margin-left: 5px;
}
h1 {
  text-align: center;
  font-size: 300%;
  color: grey;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  opacity: 30%;
  font-style: italic;
}
.checkbox-round {
  width: 1.3em;
  height: 1.3em;
  background-color: white;
  border-radius: 50%;
  vertical-align: middle;
  border: 1px solid #ddd;
  appearance: none;
  /* -webkit-appearance: none; */
  /* outline: none; */
  /* cursor: pointer; */
}
.checkbox-round:checked {
  background-color: gray;
}
footer {
  display: flex;
  justify-content: space-between;
  color: grey !important;
  line-height: 40px;
  padding: 10px;
  border: 1px solid lightgrey;
}
.check-all {
  margin: 5px;
}
</style>