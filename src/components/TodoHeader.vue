<template>
  <header class="todo__header">

    <h1 class="header">2do
      <span class="title">box</span>
    </h1>

    <section class="inputs container">
        <input
          v-model="title"
          class="input input--title"
          ref="titleInput"
          placeholder="Title"
          type="text"
          aria-label="title"
        />

        <textarea
          v-model="task"
          class="input input--task"
          @keyup.enter="createTodo"
          placeholder="Task"
          type="text"
          aria-label="task"
        />

      <button
      @click="createTodo"
      :disabled="!isEnabled"
      class="btn btn--primary"
      >
        Save
      </button>
    </section>

  </header>
</template>

<script>
export default {
  name: 'TodoHeader',

  data() {
    return {
      title: '',
      task: '',
    };
  },

  mounted() {
    this.$refs.titleInput.focus();
  },

  computed: {
    isEnabled() {
      return Boolean(this.title) && Boolean(this.task);
    },
  },

  methods: {
    createTodo() {
      const { title, task } = this;
      if (this.isEnabled) {
        const todo = {
          id: Date.now(),
          title,
          task,
          importance: 3,
          complete: false,
          created: Date.now(),
        };

        this.$refs.titleInput.focus();
        this.clearInputs();
        this.$emit('addTodo', todo);
      }
      return null;
    },

    clearInputs() {
      this.title = '';
      this.task = '';
    },
  },
};
</script>

<style lang="scss">
@import '../styles/_mixins_vars.scss';
@import '../styles/_buttons_inputs.scss';

.todo__header {
  background-color: $color-background;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
  margin-bottom: 1rem;

  .header {
    font-family: $secondary-font;
    color: $color-body-gray;
    font-size: 3rem;
    margin-bottom: 2rem;

    .title {
      color: $color-white;
      margin-left: -0.75rem;
    }

    @media screen and (max-width: 480px) {
      font-size: 2rem;
      margin-bottom: 1rem;
    }
  }

  .inputs {
    display: flex;
    flex-direction: column;
  }

  @media screen and (max-width: 480px) {
    padding: 2rem 0.5rem;
  }
}
</style>

