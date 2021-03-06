<template>
  <div class="main">
    <input
      type="text"
      class="todo-input"
      placeholder="What needs to be done"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <div v-if="todosFiltered !== 'No Todos'" class="todos">
      <transition-group
        name="custom-classes-transition"
        enter-active-class="animate__animated animate__fadeInUp"
        leave-active-class="animate__animated animate__fadeOutDown"
      >
        <div
          v-for="(todo, i) in todosFiltered"
          :key="todo.id"
          class="todo-item"
        >
          <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed" />
            <div
              v-if="!todo.editing"
              @dblclick="editTodo(todo)"
              class="todo-item-label"
              :class="{ completed: todo.completed }"
            >
              {{ todo.title }}
            </div>
            <input
              v-else
              class="todo-item-edit"
              type="text"
              v-model="todo.title"
              @blur="doneEdit(todo)"
              @keyup.enter="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)"
              v-focus
            />
          </div>
          <div @click="removeTodo(i)" class="remove-items">
            &times;
          </div>
        </div>
      </transition-group>
    </div>
    <div v-else class="todos">
      No Todos Yet
    </div>
    <div class="footer">
      <div class="check-all">
        <label for="check-all">
          <input
            type="checkbox"
            name="check-all"
            :checked="itemsChecked"
            @change="checkAll"
          />
          Check All
        </label>
      </div>
      <div class="items-left">{{ countCompleted }} Items Left</div>
      <div class="filters">
        <button :class="{ active: filter === 'all' }" @click="filter = 'all'">
          All
        </button>
        <button
          :class="{ active: filter === 'active' }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          :class="{ active: filter === 'completed' }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>
      <div class="clear-completed">
        <transition name="fade">
          <button v-if="showClearBtn" @click="clearCompleted">
            Clear Completed
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import "animate.css";

export default {
  name: "TodoList",
  data() {
    return {
      newTodo: "",
      todoId: 3,
      beforeEditCache: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "Do home work",
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: "Go to gym",
          completed: false,
          editing: false,
        },
      ],
    };
  },
  directives: {
    focus: {
      mounted: (el) => {
        el.focus();
      },
    },
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length === 0) {
        return;
      }
      this.todos.push({
        id: this.todoId,
        title: this.newTodo,
        completed: false,
        editing: false,
      });
      (this.newTodo = ""), this.todoId++;
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      if (todo.completed) {
        return;
      }
      todo.editing = true;
    },
    doneEdit(todo) {
      if (todo.title.trim() === "") {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    removeTodo(i) {
      this.todos.splice(i, 1);
    },
    checkAll(e) {
      this.todos.map((todo) => (todo.completed = e.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
  },
  computed: {
    countCompleted() {
      return this.todos.filter((el) => !el.completed).length;
    },
    itemsChecked() {
      return this.countCompleted === 0;
    },
    todosFiltered() {
      if (this.filter === "all") {
        if (this.todos.length === 0) {
          return "No Todos";
        }
        return this.todos;
      } else if (this.filter === "active") {
        if (this.todos.filter((todo) => !todo.completed).length === 0) {
          return "No Todos";
        }
        return this.todos.filter((todo) => !todo.completed);
      } else if (this.filter === "completed") {
        if (this.todos.filter((todo) => todo.completed).length === 0) {
          return "No Todos";
        }
        return this.todos.filter((todo) => todo.completed);
      } else return this.todos;
    },
    showClearBtn() {
      return this.todos.filter((todo) => todo.completed).length > 0;
    },
  },
};
</script>

<style lang="scss">
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
  &:focus {
    outline: 0;
  }
}
.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.3s;
  .remove-items {
    cursor: pointer;
    margin-left: 14px;
    &:hover {
      color: black;
    }
  }
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
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  &:focus {
    outline: none;
  }
}
.completed {
  text-decoration: line-through;
  color: gray;
}
.footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgray;
  padding-top: 20px;
  margin-top: 20px;
  button {
    font-size: 14px;
    padding: 2px 10px;
    box-shadow: none;
    border: 1px solid lightgray;
    background-color: white;
    appearance: none;
    cursor: pointer;
    &:hover {
      background: lightgreen;
    }
    &:focus {
      outline: none;
    }
  }
  .active {
    background: lightgreen;
  }
  .clear-completed {
    min-width: 125px;
    button {
      &:hover {
        background: #797b7e;
        color: white;
      }
    }
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
