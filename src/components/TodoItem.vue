<template>
  <b-list-group-item>
    <div v-if="!editing" class="todo-item">
      <!-- b-checkbox change size didn't work -->
      <input type="checkbox" class="checkbox-round" v-model="completed" />
      <!-- 还是建议加上在 class 上加 ‘’ vscode 把它渲染成变量了，虽然不影响结果，但很影响阅读 -->
      <span
        :class="['todo-text', { compeleted: completed }]"
        @dblclick="editTodo"
        >{{ title }}</span
      >
      <b-button class="remove-item" variant="light" @click="removeTodo(todo)">
        <b-icon icon="backspace"></b-icon>
      </b-button>
    </div>
    <!-- @blur is not working I don't know why, some one said use blur.native, still not working
             I find the reason: I didn't focus input at first!!! so I added autofocus on it -->
    <b-input
      v-else
      v-model="title"
      :autofocus="true"
      @blur="doneEdit"
      @keyup.enter="doneEdit"
      @keyup.esc="cancelEdit"
    ></b-input>
  </b-list-group-item>
</template>

<script>
export default {
  name: "todo-item",
  props: {
    todo: {
      type: Object,
      required: true,
    },
    checkAllTodos: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    console.log(this);
    return {
      id: this.todo.id,
      title: this.todo.title,
      completed: this.todo.completed,
      editing: this.todo.editing,
    };
  },
  computed: {
    editedTodo() {
      return {
        id: this.id,
        title: this.title,
        completed: this.completed,
        editing: this.editing,
      };
    },
  },
  watch: {
    // 监听 todoItem 中的 todo 变化，有变化就传给 parent
    editedTodo() {
      this.$emit("editTodo", this.editedTodo);
    },
    // 监听父组件参数
    checkAllTodos() {
      if (this.checkAllTodos) {
        this.completed = true;
      } else {
        this.completed = this.todo.completed;
      }
    },
    deep: true,
  },
  methods: {
    removeTodo(todo) {
      // 传送监听给 parent
      this.$emit("removeTodo", todo);
    },
    editTodo() {
      this.beforeEditCache = this.title;
      this.editing = true;
    },
    doneEdit() {
      if (this.title.trim() == "") {
        this.cancelEdit(this.todo);
        return;
      }
      this.editing = false;
    },
    cancelEdit() {
      this.title = this.beforeEditCache;
      this.editing = false;
    },
  },
};
</script>

<style>
.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.remove-item {
  color: gray;
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
</style>