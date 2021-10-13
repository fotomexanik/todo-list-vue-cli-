<template>
  <li >
    <template v-if="task.status">
      <div class="contents">
        <div class="checkbox-group">
          <input type="checkbox" @change="task.status = !task.status" checked>
          <span class="title">{{index+1}}. {{task.title}}</span>
        </div>
        <p class="description"> {{task.description}} </p>
      </div>
      <div class="btn-group">
        <button
          class="btn-remove w-100"
          @click="$emit('remove', task)"
        >Удалить</button>
<!--        <button class="btn-remove w-100" v-on:click="$emit('remove', task)">Удалить</button>-->
      </div>
    </template>

    <template v-else>
      <p class="tip" v-if="task.isEditing">Редактирование</p>
      <div class="contents" v-if="!task.isEditing">
        <div class="checkbox-group">
          <input type="checkbox" @change="task.status = !task.status">
          <span class="title">{{index+1}}. {{task.title}}</span>
        </div>
        <p class="description" v-bind:id="'description_'+ index"> {{task.description}} </p>
      </div>

      <div class="contents edit-task" v-if="task.isEditing">
        <div class="input-group d-flex">
          <span class="title">{{index+1}}.</span>
          <input
            v-model="newTitle"
            :id="'editTitle_' + index"
            :class=" newTitle ===''? 'invalid-input' : '' "
            type="text"
            placeholder="Название задачи"
            class="edit-title"
          >
        </div>
        <div class="input-group d-flex">
          <textarea
            v-model="newDescription"
            :id="'editDescription_' + task.id"
            rows="3"
            placeholder="Описание задачи"
            class="edit-description"
          ></textarea>
        </div>
      </div>

      <div class="btn-group">
        <button
          v-if="!task.isEditing"
          :disabled="editingEnabled"
          @click="$emit('toggle-edit')"
          class="btn-edit w-100"
        >Изменить</button>
        <button
          v-if="task.isEditing"
          :disabled="newTitle === '' "
          @click="$emit('save-edit')"
          class="btn-cancel w-100"
        >Сохранить</button>
        <button
          v-if="task.isEditing"
          @click="$emit('toggle-edit')"
          class="btn-cancel w-100"
        >Отменить</button>
        <button
          v-if="!task.isEditing"
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
    newTitle: String,
    newDescription: String,
    editingEnabled: Boolean
  }
}

</script>
