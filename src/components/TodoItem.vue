<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input
        type="checkbox"
        v-model="completed"
        @change="doneEdit">
      <div
        v-if="!editing"
        @click="editTodo"
        class="todo-item-label"
        :class="{ completed: completed }">{{ title }}</div>
      <input
        v-else
        class="todo-item-edit"
        type="text"
        @blur="doneEdit"
        @keyup.enter="doneEdit"
        @keyup.esc ="cancelEdit"
        v-model="title"
        v-focus
        >
    </div>
    <div class="remove-title" @click="removeTodo(index)">
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
      required: true
    },
    index: {
      type: Number,
      required: true,
    },
    checkAll: {
      type: Boolean,
      required: true
    }
  },
  data() {
     return {
       'id': this.todo.id,
       'title': this.todo.title,
       'completed': this.todo.completed,
       'editing': this.todo.editing,
       'beforeEditCache': ''
     }
  },
  watch: {
    checkAll() {
      if (this.checkAll){
        this.completed = true
      }else{
        this.completed = this.todo.completed
      }
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
    removeTodo(index){
      eventBus.$emit('removedTodo', index)
    },
    editTodo(){
      this.beforeEditCache = this.title
      this.editing = true
    },
    doneEdit() {
      if(this.title.trim().length == 0){
        this.title = this.beforeEditCache
      }
      this.editing = false
      eventBus.$emit('finishedEdit', {
        'todo': {
          'id': this.id,
          'title': this.title,
          'completed': this.completed,
          'editing': this.editing
        },
        'index': this.index
      })
    },
    cancelEdit(todo){
      this.title = this.beforeEditCache
      this.editing = false
    },
  }

}
</script>
<style>
</style>
