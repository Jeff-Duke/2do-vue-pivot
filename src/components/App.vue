<template>

  <main>
    <TodoHeader
      @addTodo="addTodo"
    />

    <section class="filter-sort-panel container">

      <button
        @click="toggleShowMore"
        class="btn btn--toggle btn--toggle--complete"
      >
        {{ show === 10 ? "Show More Todos" : "Show Fewer Todos"}}
      </button>

      <button
        @click="toggleShowComplete"
        class="btn btn--toggle btn--toggle--complete"
      >
        {{ showCompleted ? "Hide Completed Todos" : "Show Completed Todos"}}
      </button>
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

      <div class="importance-buttons">
        <label>Show Only: </label>
        <button
          @click="filterByImportance"
          :class="{toggled: importanceFilter === 5}"
          class="btn btn--toggle"
          name="5"
          :disabled="!Boolean(todos.length)"
          >Critical

        </button>
        <button
          @click="filterByImportance"
          :class="{toggled: importanceFilter === 4}"
          class="btn btn--toggle"
          :disabled="!Boolean(todos.length)"
          name="4"
          >High

        </button>
        <button
          @click="filterByImportance"
          :class="{toggled: importanceFilter === 3}"
          class="btn btn--toggle"
          name="3"
          :disabled="!Boolean(todos.length)"
          >Normal

        </button>
        <button
          @click="filterByImportance"
          :class="{toggled: importanceFilter === 2}"
          class="btn btn--toggle"
          name="2"
          :disabled="!Boolean(todos.length)"
          >Low

        </button>
        <button
          @click="filterByImportance"
          :class="{toggled: importanceFilter === 1}"
          class="btn btn--toggle"
          name="1"
          :disabled="!Boolean(todos.length)"
          >None

        </button>
      </div>

    </section>

    <section class="todo-list container">
      <Todo
        v-for="todo in visibleTodos.slice(0,show)"
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
      importanceFilter: null,
      showCompleted: false,
      show: 10,
    };
  },

  mounted() {
    this.loadStoredTodos();
    this.sortTodos();
  },

  computed: {
    filteredTodos() {
      const filter = this.filterTerm.toLowerCase();
      let filteredTodos = this.sortedTodos.filter(todo =>
        `${todo.title} ${todo.task}`.toLowerCase().includes(filter),
      );

      if (this.importanceFilter) {
        const importance = parseInt(this.importanceFilter, 10);

        filteredTodos = filteredTodos.filter(todo => todo.importance === importance);
      }

      return filteredTodos;
    },

    incompleteTodos() {
      return this.filteredTodos.filter(todo => !todo.complete);
    },

    visibleTodos() {
      if (this.showCompleted) {
        return this.filteredTodos;
      }
      return this.incompleteTodos;
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

    filterByImportance(event) {
      const importance = parseInt(event.target.name, 10);
      if (this.importanceFilter && this.importanceFilter === importance) {
        this.importanceFilter = null;
      } else {
        this.importanceFilter = importance;
      }
    },

    toggleShowComplete() {
      this.showCompleted = !this.showCompleted;
    },

    toggleShowMore() {
      this.show === 10 ? (this.show = this.visibleTodos.length) : (this.show = 10);
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
