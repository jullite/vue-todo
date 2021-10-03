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
      <b-list-group v-for="(todo, index) in todos" :key="todo.id">
        <b-list-group-item>
          <div v-if="!todo.editing" class="todo-item">
            <!-- b-checkbox change size didn't work -->
            <input
              type="checkbox"
              class="checkbox-round"
              v-model="todo.completed"
            />
            <!-- 还是建议加上在 class 上加 ‘’ vscode 把它渲染成变量了，虽然不影响结果，但很影响阅读 -->
            <span
              :class="['todo-text', { compeleted: todo.completed }]"
              @dblclick="editTodo(todo)"
              >{{ todo.title }}</span
            >
            <b-button
              class="remove-item"
              variant="light"
              @click="removeTodo(index)"
            >
              <b-icon icon="backspace"></b-icon>
            </b-button>
          </div>
          <!-- @blur is not working I don't know why, some one said use blur.native, still not working-->
          <!-- I find the reason: I didn't focus input at first!!! so I added autofocus on it -->
          <b-input
            v-else
            v-model="todo.title"
            :autofocus="true"
            @blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            @keyup.esc="cancelEdit(todo)"
          ></b-input>
          <!-- <b-button class="remove-item" variant="light">x</b-button> -->
        </b-list-group-item>
      </b-list-group>
    </section>
    <footer>
      <span>{{ leftTodos() }} items left</span>
      <b-button variant="outline-secondary" @click="clearCompleted"
        >clear completed</b-button
      >
    </footer>
  </div>
</template>

<script>
export default {
  name: "todo-list",
  data() {
    return {
      newTodo: "",
      idForTodo: 3,
      beforeEditCache: "",
      todos: [
        {
          id: 1,
          title: "done vue-app",
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: "take over the world",
          completed: false,
          editing: false,
        },
      ],
    };
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.unshift({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false,
      });
      this.newTodo = "";
      this.idForTodo++;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
      console.log("removed todo");
    },

    // 本来想写成一个 function 叫 changeEdit 但是发现监听事件触发这个方法被调用了两次
    // search 感觉它们说的原因都不太合理，所以最好的办法还是写成两个 function 吧
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
      console.log(todo.editing);
    },
    leftTodos() {
      let sum = 0;
      this.todos.forEach((todo) => {
        if (todo.completed) {
          sum++;
        }
      });
      return sum;
    },
    clearCompleted() {
      this.todos.forEach((todo, index) => {
        if (todo.completed) {
          this.removeTodo(index);
        }
      });
    },
    doneEdit(todo) {
      if (todo.title.trim() == "") {
        this.cancelEdit(todo);
        return;
      }
      todo.editing = false;
      console.log(todo.editing);
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
  },
};
</script>


<style>
.container {
  margin-top: 20px;
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
.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.remove-item {
  color: gray;
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
.todo-text {
  font-size: 20px;
  width: 80%;
  overflow: hidden;
}
.compeleted {
  text-decoration-line: line-through;
  color: grey;
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
</style>