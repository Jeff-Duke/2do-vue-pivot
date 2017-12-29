<template>

  <main>
    <TodoHeader
      @addTodo="addTodo"
    />

    <section class="filter-sort-panel container">
        <input
          v-model="filterTerm"
          class="input input--filter"
          placeholder="Filter"
          aria-label="Filter"
        />

      <label for="sort">Sort By:
        <select
        v-model="sortBy"
        class="select select--sort"
        @change="sortTodos"
        >
          <option value="newest" selected>Newest</option>
          <option value="oldest">Oldest</option>
          <option value="highest">Highest Importance</option>
          <option value="lowest">Lowest Importance</option>
        </select>
      </label>
    </section>

    <section class="todo-list container">
      <Todo
        v-for="todo in filteredTodos"
        :key="todo.id"
        :todo="todo"
        @deleteTodo="deleteTodo"
        @updateTodo="updateTodo"
      />

    </section>
  </main>

</template>

<script>
import TodoHeader from './TodoHeader';
import Todo from './Todo';

const sorters = {
  newest: todos => todos.sort((a, b) => a.created < b.created),
  oldest: todos => todos.sort((a, b) => a.created > b.created),
  highest: todos => todos.sort((a, b) => a.importance < b.importance),
  lowest: todos => todos.sort((a, b) => a.importance > b.importance),
};

export default {
  name: 'App',
  components: {
    TodoHeader,
    Todo,
  },

  data() {
    return {
      todos: [],
      filterTerm: '',
      sortBy: 'newest',
      sortedTodos: [],
    };
  },

  mounted() {
    this.loadStoredTodos();
    this.sortTodos();
  },

  computed: {
    filteredTodos() {
      return this.sortedTodos.filter(
        todo =>
          todo.title.toLowerCase().includes(this.filterTerm.toLowerCase()) ||
          todo.task.toLowerCase().includes(this.filterTerm.toLowerCase()),
      );
    },
  },

  methods: {
    addTodo(todo) {
      this.todos.unshift(todo);
      this.storeTodos();
    },

    updateTodo(todo) {
      const index = this.todos.findIndex(storedTodo => storedTodo.id === todo.id);
      this.todos[index] = todo;

      this.storeTodos();
      this.sortTodos();
    },

    deleteTodo(todoID) {
      const id = parseInt(todoID, 10);

      this.todos = this.todos.filter(todo => todo.id !== id);
      this.storeTodos();
      this.sortTodos();
    },

    storeTodos() {
      localStorage.setItem('todos', JSON.stringify(this.todos));
    },

    loadStoredTodos() {
      this.todos = JSON.parse(localStorage.getItem('todos')) || [];
    },

    sortTodos() {
      const sortedTodos = sorters[this.sortBy](this.todos);
      this.sortedTodos = sortedTodos;
    },
  },
};
</script>

<style lang='scss'>
@import '../styles/_reset.scss';
@import '../styles/_mixins_vars.scss';
@import '../styles/_buttons_inputs.scss';

body {
  background-color: $color-white;
  font-family: $primary-font;
  margin: 0;
}

.container {
  margin: 0 auto;
  width: 65vw;

  @media screen and(max-width: 480px) {
    width: 90vw;
  }
}

.filter-sort-panel {
  display: flex;
  flex-direction: column-reverse;
  margin-top: 2rem;

  .select--sort {
    appearance: none;
    background: url('../assets/chevron-down.png') no-repeat 95%;
    background-size: 1.25rem;
    border: 2px solid $color-border-gray;
    font-size: 1rem;
    margin: 1rem 0 1.5rem 0.75rem;
    padding: 0.25rem;
    padding-right: 2rem;
  }
}
</style>
