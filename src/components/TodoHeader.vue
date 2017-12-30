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
        <span
          class="input__character-counter"
        >
          {{`${charactersLeft}/120`}}
        </span>

      <button
      @click="createTodo"
      :disabled="!isEnabled"
      class="btn btn--primary"
      >
        Save
      </button>
    </section>
    <p v-show="error" class="input--error">{{error}}</p>
  </header>
</template>

<script>
export default {
  name: 'TodoHeader',

  data() {
    return {
      title: '',
      task: '',
      error: '',
    };
  },

  mounted() {
    this.$refs.titleInput.focus();
  },

  computed: {
    isEnabled() {
      return Boolean(this.title) && Boolean(this.task) && Boolean(this.charactersLeft >= 0);
    },

    charactersLeft() {
      return 120 - this.task.length;
    },
  },

  methods: {
    createTodo() {
      this.error = '';

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
      } else {
        this.error = 'We were unable to submit that todo';
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

  .input--task {
    margin-bottom: 0;
  }

  .input__character-counter {
    color: $color-white;
    font-size: 1.125rem;
    margin-bottom: 1rem;
  }

  .input--error {
    color: $color-white;
    font-size: 1.25rem;
    margin-top: 1rem;
  }

  @media screen and (max-width: 480px) {
    padding: 2rem 0.5rem;
  }
}
</style>

