<template>
  <div id="app-todo">
    <div class="header">
      <div class="container">
        <div class="logo">ToDo List</div>
        <div class="form">
          <div class="input-group">
            <input type="text" v-model="valueTitle" v-on:input="handlyTitle" v-on:keypress.enter="addTask" placeholder="Название задачи">
            <textarea type="text" v-model="valueDescription"  rows="4" placeholder="Описание задачи" disabled="true"></textarea>
          </div>
          <div class="btn-group">
            <button class="btn" id="btnAddTask" v-on:click="addTask">Добавить задачу</button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <h2>
        <span>Текущие задачи</span>
        <span class="count-tasks">{{currentDoList.length}}</span>
      </h2>
      <ul class="task-list">
        <li
          v-for="(task, index) in currentDoList"
          :key="task.id"
          v-bind:id="'currentTaskEl_' + index"
          v-bind:class="{active : task.isEditing }"
        >
          <p class="tip" v-show="task.isEditing">Редактирование</p>
          <div class="contents" v-show="!task.isEditing">
            <div class="checkbox-group">
              <input type="checkbox" v-on:change="doCheck(index, 'current')">
              <span class="title">{{index+1}}. {{task.title}} </span>
            </div>
            <p class="description" v-bind:id="'description_'+ index"> {{task.description}} </p>
          </div>
          <div class="contents edit-task" v-show="task.isEditing">
            <div class="input-group d-flex">
              <span class="title">{{index+1}}.</span>
              <input
                v-model="newTitle"
                v-on:input="checkEditTitle(index)"
                v-bind:id="'editTitle_' + index"
                v-bind:class=" newTitle ===''? 'border-red' : '' "
                type="text"
                placeholder="Название задачи"
                class="edit-title"
              >
            </div>
            <div class="input-group d-flex">
                        <textarea
                          v-model="newDescription"
                          v-bind:id="'editDescription_' + index"
                          rows="1"
                          placeholder="Описание задачи"
                          class="edit-description"
                        ></textarea>
            </div>
          </div>

          <div class="btn-group">
            <button class="btn-edit w-100" v-show="!task.isEditing" v-on:click="toggleEdit(index)">Изменить</button>
            <button class="btn-cancel w-100" v-show="task.isEditing" v-bind:id="'saveEdit_' + index" v-on:click="saveEdit(index)">Сохранить</button>
            <button class="btn-cancel w-100" v-show="task.isEditing" v-on:click="toggleEdit(index)">Отменить</button>
            <button class="btn-remove w-100" v-show="!task.isEditing" v-on:click="removeTask(index, 'current')">Удалить</button>
          </div>

        </li>
      </ul>
      <h2>
        <span>Выполнение задачи</span>
        <span class="count-tasks done">{{doneDoList.length}}</span>
      </h2>
      <ul class="task-list complete-list">
        <li v-for="(task, index) in doneDoList" :key="task.id" v-bind:id="'doneTaskEl_' + index">
          <div class="contents">
            <div class="checkbox-group">
              <input type="checkbox" v-on:change="doCheck(index, 'done')" checked>
              <span class="title"> {{index+1}}. {{task.title}} </span>
            </div>
            <p class="description"> {{task.description}} </p>
          </div>
          <div class="btn-group">
            <!--                    <button class="btn-remove w-100" disabled="true">Изменить</button>-->
            <!--                    <button class="btn-remove w-100">Сохранить</button>-->
            <button class="btn-remove w-100" v-on:click="removeTask(index, 'done')">Удалить</button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
const fakeCurrentDoList = [
  {
    title: 'Работа с классами и стилями',
    description: 'Часто возникает необходимость динамически изменять CSS-классы и inline-стили элементов в зависимости от состояния приложения. Поскольку и то и другое атрибуты, мы можем использовать v-bind: необходимо лишь вычислить итоговую строку при помощи выражения. ',
    id: 1,
    isEditing: false
  },
  {
    title: 'Вычисляемые свойства и слежение\n',
    description: 'Встраиваемые в шаблоны выражения удобны, но могут предназначаться только для простых операций. При усложнении логики их труднее поддерживать.',
    id: 2,
    isEditing: false
  },
  {
    title: 'новая Задача3',
    description: 'Описание задачи3 Описание задачи3 Описание задачи3',
    id: 3,
    isEditing: false
  }
]
const fakeDoneDoList = [
  {
    title: 'Отображение массива элементов с помощью v-for',
    description: 'Используйте директиву v-for для отрисовки списка элементов на основе массива данных. У директивы v-for особый синтаксис записи: item in items, где items — исходный массив, а item — ссылка на текущий элемент массива:',
    id: 51,
    isEditing: false
  },
  {
    title: 'выполненая Задача2',
    description: 'Описание задачи2 Описание задачи2 Описание задачи2',
    id: 52,
    isEditing: false
  },
  {
    title: 'выполненая Задача3',
    description: 'Описание задачи3 Описание задачи3 Описание задачи3',
    id: 53,
    isEditing: false
  },
  {
    title: 'выполненая Задача4',
    description: 'Описание задачи4 Описание задачи4',
    id: 54,
    isEditing: false
  }
]

export default {
  name: 'app-todo',
  data () {
    return {
      valueTitle: '',
      valueDescription: '',
      newTitle: '',
      newDescription: '',
      currentDoList: fakeCurrentDoList,
      doneDoList: fakeDoneDoList

    }
  },
  methods: {
    handlyTitle () {
      // eslint-disable-next-line eqeqeq
      if (this.valueTitle !== '') {
        document.querySelector('textarea').disabled = false
      }
    },
    addTask () {
      if (this.valueTitle === '') { return };
      this.currentDoList.push({
        title: this.valueTitle,
        description: this.valueDescription,
        id: Math.random(),
        isEditing: false
      })
      this.valueTitle = ''
      this.valueDescription = ''
      document.querySelector('textarea').disabled = true
    },
    doCheck (index, type) {
      if (type === 'current') {
        const doneTask = this.currentDoList.splice(index, 1)
        this.doneDoList.push(...doneTask)
      } else {
        const currentTask = this.doneDoList.splice(index, 1)
        this.currentDoList.push(...currentTask)
      }
    },
    removeTask (index, type) {
      const nameList = type === 'current' ? 'ТЕКУЩИХ' : 'ВЫПОЛНЕНЫХ'
      const confirmRemove = confirm(`Удалить задачу №${index + 1} из списка ${nameList} задач?`)
      if (confirmRemove) {
        const toDoList = type === 'current' ? this.currentDoList : this.doneDoList
        toDoList.splice(index, 1)
      }
    },
    setNewParams (title, description) {
      console.log('setNewParams')
      this.newTitle = title
      this.newDescription = description
    },
    resetNewParams () {
      console.log('resetNewParams')
      this.newTitle = ''
      this.newDescription = ''
    },
    toggleEdit (index) {
      const editTask = this.currentDoList[index]
      const btnsEdit = Array.from(document.querySelectorAll('.btn-edit'))
      const btnAddTask = document.getElementById('btnAddTask')
      editTask.isEditing = !editTask.isEditing

      if (editTask.isEditing) {
        // включен режим редактирования
        this.setNewParams(editTask.title, editTask.description)
        // document.getElementById(`editTitle_${index}`).focus(); // TODO не работает focus???
        this.setHeightTextarea(index)
        btnAddTask.disabled = true
        btnsEdit.forEach((btnEdit) => { btnEdit.disabled = true })
      } else {
        // выключаем режим редактирования сброс изменений
        btnAddTask.disabled = false
        btnsEdit.forEach((btnEdit) => { btnEdit.disabled = false })
        this.resetNewParams()
      }
    },
    saveEdit (index) {
      const editTask = this.currentDoList[index]
      const btnsEdit = Array.from(document.querySelectorAll('.btn-edit'))
      const btnAddTask = document.getElementById('btnAddTask')
      if (this.newTitle !== '') {
        editTask.title = this.newTitle
        editTask.description = this.newDescription
        editTask.isEditing = !editTask.isEditing
        this.resetNewParams()
        btnAddTask.disabled = false
        btnsEdit.forEach((btnEdit) => { btnEdit.disabled = false })
      }
    },
    checkEditTitle (index) {
      const btnSave = document.getElementById(`saveEdit_${index}`)
      if (this.newTitle === '') {
        btnSave.disabled = true
      } else {
        btnSave.disabled = false
      }
    },
    setHeightTextarea (index) {
      const elDescription = document.getElementById(`description_${index}`)
      const textarea = document.getElementById(`editDescription_${index}`)
      textarea.setAttribute('style', 'height:' + (elDescription.scrollHeight) + 'px;overflow-y:hidden;')
      textarea.addEventListener('input', () => { textarea.style.height = (textarea.scrollHeight) + 'px' }, false)
    }
  }

}
</script>

<style lang="scss">
@import "assets/style.css";
</style>
