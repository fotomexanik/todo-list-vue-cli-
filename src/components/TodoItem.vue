<template>
  <li :class=" editingEnabled ? 'disabled' : '' ">
    <template v-if="task.status">
      <div class="contents">
        <div class="checkbox-group">
          <LabelCheckbox
            :evtName="'toggle-status'"
            :status="task.status"
            :editingEnabled="editingEnabled"
            @toggle-status="task.status = !task.status"
          ></LabelCheckbox>
          <span class="size-m">{{index+1}}. {{task.title}}</span>
        </div>
        <p class="description"> {{task.description}} </p>
      </div>
      <div class="btn-group">
        <BtnAction
          :disabled="editingEnabled"
          :btnName="'Удалить'"
          :evtName="'remove-task'"
          @remove-task="$emit('remove-task')"
        ></BtnAction>
      </div>
    </template>

    <template v-else>
      <p class="tip" v-if="task.isEditing">Редактирование</p>
      <div class="contents" v-if="!task.isEditing">
        <div class="checkbox-group">
          <LabelCheckbox
            :evtName="'toggle-status'"
            :status="task.status"
            :editingEnabled="editingEnabled"
            @toggle-status="task.status = !task.status"
          ></LabelCheckbox>
          <span class="size-m" @dblclick="!editingEnabled ? toggleEdit(task) : '' ">{{index+1}}. {{task.title}}</span>
        </div>
        <p class="description"> {{task.description}} </p>
      </div>

      <div class="contents edit-task" v-if="task.isEditing">
        <div class="input-group d-flex">
          <span class="size-m">{{index+1}}.</span>
            <InputEdit
              :new-title="newTitle"
              @update:newTitle="newTitle = $event"
              @save-edit="saveEdit(task)"
              @toggle-edit="toggleEdit(task)"
              :class="newTitle.trim() ===''? 'invalid-input' : '' "
              class="edit-title"
              placeholder="Название задачи"
            ></InputEdit>
        </div>
        <div class="input-group d-flex">
          <TextareaEdit
            :new-description="newDescription"
            @update:newDescription="newDescription = $event"
            @toggle-edit="toggleEdit(task)"
            class="edit-description"
            placeholder="Описание задачи"
            row="1"
          ></TextareaEdit>
        </div>
      </div>

      <div class="btn-group">
        <BtnAction
          v-if="!task.isEditing"
          :disabled="editingEnabled"
          :btn-name="'Изменить'"
          :evt-name="'toggle-edit'"
          @toggle-edit="toggleEdit(task)"
          class="btn-action w-100"
        ></BtnAction>
        <BtnAction
          v-if="task.isEditing"
          :disabled="newTitle.trim() === '' || newTitle === task.title && newDescription === task.description  "
          :btn-name="'Сохранить'"
          :evt-name="'save-edit'"
          @save-edit="saveEdit(task)"
          class="btn-action w-100"
        ></BtnAction>
        <BtnAction
          v-if="task.isEditing"
          :btn-name="'Отменить'"
          :evt-name="'toggle-edit'"
          @toggle-edit="toggleEdit(task)"
          class="btn-action w-100"
        ></BtnAction>
        <BtnAction
          v-if="!task.isEditing"
          :disabled="editingEnabled"
          :btn-name="'Удалить'"
          :evt-name="'remove-task'"
          @remove-task="$emit('remove-task')"
          class="btn-action w-100"
        ></BtnAction>
      </div>
    </template>
  </li>
</template>

<script>
import TextareaEdit from '@/components/TextareaEdit'
import InputEdit from '@/components/InputEdit'
import BtnAction from '@/components/BtnAction'
import LabelCheckbox from '@/components/LabelCheckbox'
export default {
  components: {
    TextareaEdit, InputEdit, BtnAction, LabelCheckbox
  },
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
    }
  }
}

</script>
