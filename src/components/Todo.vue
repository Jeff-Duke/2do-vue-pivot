<template>
  <article class="todo-card">
    <h3
      @dblclick="editTodo('todoTitle')"
      v-if="editing !==
      'todoTitle'"
      class="todo__title"
    >
      {{todo.title}}
    </h3>

    <input class="edit edit__title" type="text"
      v-model="title"
      v-if="editing === 'todoTitle'"
      @blur="doneEditing"
      @keyup.enter="doneEditing"
      ref="todoTitle"
    />

    <button
      @click="deleteTodo"
      class="btn__todo btn__todo--delete"
      aria-label="delete todo"
    />

    <p
      @dblclick="editTodo('todoTask')"
      v-if="editing !== 'todoTask'"
      class="todo__task"
    >
      {{todo.task}}
    </p>

    <textarea class="edit edit__task" type="text"
      v-model="task"
      v-if="editing === 'todoTask'"
      @blur="doneEditing"
      @keyup.enter="doneEditing"
      ref="todoTask"
    />

    <div>
      <p class="todo__quality">Quality: <span class="todo__quality--text">{{ todoQuality }}</span></p>

      <button
        @click="upVote(todo)"
        :disabled="quality === 3"
        class="btn btn__todo btn__todo--upvote"
        aria-label="upvote todo"
      />

      <button
        @click="downVote(todo)"
        :disabled="quality === 1"
        class="btn btn__todo btn__todo--downvote"
        aria-label="downvote todo"
      />
    </div>

  </article>
</template>

<script>
export default {
  name: 'Todo',

  props: {
    todo: {
      type: Object,
      required: true,
    },
  },

  data() {
    return {
      title: this.todo.title,
      task: this.todo.task,
      quality: this.todo.quality,
      editing: false,
    };
  },

  computed: {
    todoQuality() {
      const qualityGate = {
        1: 'swill',
        2: 'plausible',
        3: 'genius',
      };

      return qualityGate[this.quality];
    },
  },

  methods: {
    upVote() {
      this.quality += 1;
      this.updateTodo();
    },

    downVote() {
      this.quality -= 1;
      this.updateTodo();
    },

    deleteTodo() {
      return this.$emit('deleteTodo', this.todo.id);
    },

    updateTodo() {
      const { id, created } = this.todo;
      const { title, task, quality } = this;
      const todo = {
        id,
        title,
        task,
        quality,
        created,
      };

      return this.$emit('updateTodo', todo);
    },

    editTodo(ref) {
      this.editing = ref;
      this.$nextTick(() => {
        this.$refs[ref].focus();
      });
    },

    doneEditing() {
      this.editing = false;
      this.updateTodo();
    },
  },
};
</script>

<style lang="scss">
@import '../styles/_mixins_vars.scss';

.todo-card {
  border-bottom: 1px solid $color-border-gray;
  color: $color-text-dark-gray;
  font-family: $primary-font;
  font-size: 1rem;
  margin: 1rem 0;
  padding: 1rem 0 3rem 0;

  .todo__title,
  .todo__quality {
    font-family: $secondary-font;
    font-weight: 700;
    color: $color-text-dark-gray;
  }

  .todo__title,
  .todo__task {
    margin-bottom: 1rem;
  }

  .todo__title {
    font-size: 1.5rem;
  }

  .todo__task {
    line-height: 1.5rem;
    width: 85%;
  }

  .edit {
    display: block;
    border: 2px solid $color-border-gray;
    font-family: $primary-font;
    font-size: 1.125rem;
    margin-bottom: 1rem;
    padding: 0.5rem;

    &__title,
    &__task {
      width: 85%;
    }

    &__task {
      resize: none;
      height: 6rem;
    }
  }

  .btn__todo {
    border: none;
    background-color: transparent;
    cursor: pointer;
    height: 1.5rem;
    width: 1.5rem;

    &--delete {
      float: right;
      background: url('../assets/delete.svg') center no-repeat;

      &:hover {
        background: url('../assets/delete-hover.svg') center no-repeat;
      }
    }

    &--upvote {
      float: left;
      background: url('../assets/upvote.svg') center no-repeat;
      margin-right: 0.5rem;

      &:hover {
        background: url('../assets/upvote-hover.svg') center no-repeat;
      }
    }

    &--downvote {
      float: left;
      background: url('../assets/downvote.svg') center no-repeat;
      margin-right: 1rem;

      &:hover {
        background: url('../assets/downvote-hover.svg') center no-repeat;
      }
    }
  }

  .todo__quality {
    margin: 1rem 0;
  }
}
</style>

