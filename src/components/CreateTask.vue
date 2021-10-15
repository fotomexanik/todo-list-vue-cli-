<template>
  <div class="form">
    <div class="input-group">
      <input
        type="text"
        v-model.trim="title"
        @keypress.enter="addTask"
        @keyup.27= "setTitle('')"
        :disabled="editingEnabled"
        placeholder="Название задачи"
      >
      <textarea
        v-model.trim="description"
        @keyup.27= "setDescription('')"
        :disabled="title === '' ||  editingEnabled"
        rows="4"
        placeholder="Описание задачи"
      ></textarea>
    </div>
    <div class="btn-group">
      <BtnAction
        :disabled="title === '' ||  editingEnabled"
        :btnName="'Добавить задачу'"
        :evtName="'add-task'"
        @add-task="addTask"
        class="btn-add"
      >
      </BtnAction>
    </div>
  </div>
</template>

<script>
import BtnAction from '@/components/BtnAction'
export default {
  components: {
    BtnAction
  },
  props: {
    editingEnabled: Boolean
  },
  data () {
    return {
      title: '',
      description: ''
    }
  },
  methods: {
    setTitle (title) {
      console.log('setTitle')
      this.title = title
    },
    setDescription (description) {
      console.log('setDescription')
      this.description = description
    },
    addTask () {
      console.log('addTask компонент')
      const title = this.title.trim()
      const description = this.description.trim()
      if (title !== '') {
        this.$emit('add-task', title, description)
        this.setTitle('')
        this.setDescription('')
      }
    }
  }
}

</script>
