<template>
  <li :class=" editingEnabled ? 'disabled' : '' ">
    <template v-if="task.status">
      <div class="contents">
        <div class="checkbox-group">
          <input
            type="checkbox"
            @change="task.status = !task.status"
            :disabled="editingEnabled"
            checked
            >
          <span class="title">{{index+1}}. {{task.title}}</span>
        </div>
        <p class="description"> {{task.description}} </p>
      </div>
      <div class="btn-group">
        <button
          class="btn-remove w-100"
          @click="$emit('remove', task)"
          :disabled="editingEnabled"
        >Удалить</button>
<!--        <button class="btn-remove w-100" v-on:click="$emit('remove', task)">Удалить</button>-->
      </div>
    </template>

    <template v-else>
      <p class="tip" v-if="task.isEditing">Редактирование</p>
      <div class="contents" v-if="!task.isEditing">
        <div class="checkbox-group">
          <input
            type="checkbox"
            @change="task.status = !task.status"
            :disabled="editingEnabled">
          <span class="title">{{index+1}}. {{task.title}}</span>
        </div>
        <p class="description" v-bind:id="'description_'+ index"> {{task.description}} </p>
      </div>

      <div class="contents edit-task" v-if="task.isEditing">
        <div class="input-group d-flex">
          <span class="title">{{index+1}}.</span>
          <input
            v-model="newTitle"
            @keypress.enter="saveEdit(task)"
            @keyup.27="toggleEdit(task)"
            :class="newTitle.trim() ===''? 'invalid-input' : '' "
            type="text"
            placeholder="Название задачи"
            class="edit-title"
          >
        </div>
        <div class="input-group d-flex">
          <textarea
            v-model="newDescription"
            @keyup.27="toggleEdit(task)"
            @input ="setHeightTextarea"
            placeholder="Описание задачи"
            row="1"
            class="edit-description"
          ></textarea>
        </div>
      </div>

      <div class="btn-group">
        <button
          v-if="!task.isEditing"
          :disabled="editingEnabled"
          @click="toggleEdit(task)"
          class="btn-edit w-100"
        >Изменить</button>
        <button
          v-if="task.isEditing"
          :disabled="newTitle.trim() === '' || newTitle === task.title && newDescription === task.description  "
          @click="saveEdit(task)"
          class="btn-cancel w-100"
        >Сохранить</button>
        <button
          v-if="task.isEditing"
          @click="toggleEdit(task)"
          class="btn-cancel w-100"
        >Отменить</button>
        <button
          v-if="!task.isEditing"
          :disabled="editingEnabled"
          @click="$emit('remove')"
          class="btn-remove w-100"
        >Удалить</button>
      </div>
    </template>
  </li>
</template>

<script>
export default {
  props: {
    task: {
      type: Object,
      required: true
    },
    index: Number,
    editingEnabled: Boolean
  },
  data () {
    return {
      newTitle: '',
      newDescription: ''
    }
  },
  methods: {
    setNewParams (title, description) {
      console.log('setNewParams')
      this.newTitle = title
      this.newDescription = description
    },
    toggleEdit (editTask) {
      console.log('TodoItem - toggleEdit ')
      console.log(editTask.isEditing)
      if (editTask.isEditing) {
        // console.log('сброс title и description')
        this.setNewParams('', '')
      } else {
        // console.log('копируем данные в newTitle и newDescription')
        this.setNewParams(editTask.title, editTask.description)
      }
      // переключение режима реддактироания
      this.$emit('toggle-edit')
      // console.log(editTask.isEditing)
      if (editTask.isEditing) {
        this.$nextTick(function () {
          // теперь DOM обновлён
          const input = document.querySelector('.edit-title')
          const textarea = document.querySelector('.edit-description')
          input.focus()
          textarea.style.height = textarea.scrollHeight + 'px'
          textarea.style.overflowY = 'hidden'
        })
      }
    },
    saveEdit (editTask) {
      // console.log('TodoItem - saveEdit')
      const newTitle = this.newTitle.trim()
      const newDescription = this.newDescription.trim()
      if (newTitle !== '') {
        this.$emit('save-edit', editTask, newTitle, newDescription)
        this.setNewParams('', '')
        this.$emit('toggle-edit')
      }
    },
    setHeightTextarea (event) {
      // console.log('setHeightTextarea')
      const textarea = event.target
      textarea.style.height = textarea.scrollHeight + 'px'
      // textarea.style.overflowY = 'hidden'
    }

  }
}

</script>
