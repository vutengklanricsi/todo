<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="completed" @change="doneEdit">

      <div v-if="!editing" @dblclick="editTodo" class="todo-item-label"
      :class="{ completed : completed }">{{title}}</div>

    <input v-else
      class="todo-item-edit"
      type="text"
      v-model="title"
      @blur="doneEdit"
      @keyup.enter="doneEdit"
      @keyup.escape="cancelEdit"
      v-focus>
  </div>
  <div class="remove-item" @click="removeTodo(index)">
    &times;
  </div>

  </div>
</template>

<script>
export default {
  name: 'todo-item',
  props: {
    todo: {
      type: Object,
      required: true, // biztosítja, hogy megkapjuk a todo props-ot és ha nincs akkor akkor a vue szól, hogy valami nincs rendben.
    },
    index: {
      type: Number,
      required: true,
    },
    checkAll: {
      type: Boolean,
      required: true,
    }
  },
  data() {
    return {
      'id': this.todo.id,
      'title': this.todo.title,
      'completed': this.todo.completed,
      'editing': this.todo.editing,
      'beforeEditCache': '',
    }
  },
  watch: {
    checkAll() {
    this.completed = this.checkAll ? true : this.todo.completed
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },
  methods: {
    removeTodo(index) {
      this.$emit('removedTodo', index)
    },
    editTodo() {
      this.beforeEditCache = this.title
      this.editing = true
    },
    doneEdit() {
      if (this.title.trim().length == '') { // nem hagyja, hogy üres mezőt adjunk hozzá a tömbhöz
        this.title = this.beforeEditCache
      }
      this.editing = false;
      this.$emit('finishedEdit', {
        index: this.index,
        todo: {
          'id': this.id,
          'title': this.title,
          'completed': this.completed,
          'editing': this.editing,
        }
      })
    },
    cancelEdit() {
      this.title = this.beforeEditCache // grab the title before we editing it
      this.editing = false;
    },
  }
}
</script>

<style>

</style>