<template>
  <article class="todo-card">
    <h3
      @dblclick="editTodo('todoTitle')"
      v-if="editing !==
      'todoTitle'"
      class="todo__title"
      :class="{ complete: todo.complete }"
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
      :class="{ complete: todo.complete }"
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
      <p
        class="todo__importance"
        :class="{ complete: todo.complete }"
      >Importance: <span class="todo__importance--text">{{ todoImportance }}</span></p>

      <button
        @click="upVote(todo)"
        :disabled="importance === 5"
        class="btn btn__todo btn__todo--upvote"
        aria-label="upvote todo"
      />

      <button
        @click="downVote(todo)"
        :disabled="importance === 1"
        class="btn btn__todo btn__todo--downvote"
        aria-label="downvote todo"
      />
    </div>
    <label
        class="todo__complete--label"
      >
        <span class="todo__complete--complete" v-if="todo.complete">Completed</span>
        <span class="todo__complete--incomplete" v-if="!todo.complete">Incomplete</span>

        <input
          :checked="todo.complete"
          type="checkbox"
          @click="toggleTodoComplete"
          class="todo__complete"
        />
      </label>

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
      importance: this.todo.importance,
      complete: this.todo.complete,
      editing: false,
    };
  },

  computed: {
    todoImportance() {
      const importanceGate = {
        1: 'none',
        2: 'low',
        3: 'normal',
        4: 'high',
        5: 'critical',
      };

      return importanceGate[this.importance];
    },
  },

  methods: {
    upVote() {
      this.importance += 1;
      this.updateTodo();
    },

    downVote() {
      this.importance -= 1;
      this.updateTodo();
    },

    deleteTodo() {
      return this.$emit('deleteTodo', this.todo.id);
    },

    updateTodo() {
      const { id, created } = this.todo;
      const { title, task, importance, complete } = this;
      const todo = {
        id,
        title,
        task,
        importance,
        complete,
        created,
      };

      return this.$emit('updateTodo', todo);
    },

    toggleTodoComplete() {
      this.complete = !this.complete;
      this.updateTodo();
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
  .todo__importance {
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
    display: inline-block;
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

  .todo__importance {
    margin: 1rem 0;

    &.complete {
      visibility: hidden;
    }
  }

  .complete {
    color: $color-body-gray;
    text-decoration: line-through;
  }
}
</style>

