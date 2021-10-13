<template>
  <div id="app-todo">
    <div class="header">
      <div class="container">
        <div class="logo">ToDo List</div>
        <div class="form">
          <div class="input-group">
            <input type="text" v-model="valueTitle" @keypress.enter="addTask" placeholder="Название задачи">
            <textarea type="text" v-model="valueDescription"  rows="4" placeholder="Описание задачи" :disabled="valueTitle === ''"></textarea>
          </div>
          <div class="btn-group">
            <button class="btn" id="btnAddTask" @click="addTask" :disabled="editingEnabled">Добавить задачу</button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <template v-if="currentDoList.length > 0">
        <h2>
          <span>Текущие задачи</span>
          <span class="count-tasks">{{currentDoList.length}}</span>
        </h2>
        <ul class="task-list">
          <TodoItem
            v-for="(task, index) in currentDoList"
            :task="task"
            :index="index"
            :key="task.id"
            :editingEnabled="editingEnabled"
            :id="'currentTaskEl_' + index"
            :class="{active : task.isEditing }"
            @remove="removeTask(task)"
            @toggle-edit="toggleEdit(task)"
            @save-edit="saveEdit"
          />
        </ul>
      </template>
      <template v-if="doneDoList.length > 0">
        <h2>
          <span>Выполнение задачи</span>
          <span class="count-tasks done">{{doneDoList.length}}</span>
        </h2>
        <ul class="task-list complete-list">
          <TodoItem
            v-for="(task, index) in doneDoList"
            :task="task"
            :index="index"
            :key="task.id"
            :id="'doneTaskEl_' + index"
            @remove="removeTask(task)"
          />
        </ul>
      </template>

    </div>
  </div>
</template>

<script>
import TodoItem from '@/components/TodoItem'
const fakeData = [
  {
    title: 'новая Задача 1. Работа с классами и стилями',
    description: 'Часто возникает необходимость динамически изменять CSS-классы и inline-стили элементов в зависимости от состояния приложения. Поскольку и то и другое атрибуты, мы можем использовать v-bind: необходимо лишь вычислить итоговую строку при помощи выражения. ',
    id: 1,
    isEditing: false,
    status: false
  },
  {
    title: 'новая Задача 2. Вычисляемые свойства и слежение',
    description: 'Встраиваемые в шаблоны выражения удобны, но могут предназначаться только для простых операций. При усложнении логики их труднее поддерживать.',
    id: 2,
    isEditing: false,
    status: false
  },
  {
    title: 'новая Задача 3',
    description: 'Описание задачи3',
    id: 3,
    isEditing: false,
    status: false
  },
  {
    title: 'выполненая Задача 1. Отображение массива элементов',
    description: 'Используйте директиву v-for для отрисовки списка элементов на основе массива данных. У директивы v-for особый синтаксис записи: item in items, где items — исходный массив, а item — ссылка на текущий элемент массива:',
    id: 4,
    isEditing: false,
    status: true
  },
  {
    title: 'выполненая Задача 2',
    description: 'Описание задачи2 Описание задачи2 Описание задачи2',
    id: 52,
    isEditing: false,
    status: true
  },
  {
    title: 'выполненая Задача 3',
    description: 'Описание задачи3 Описание задачи3 Описание задачи3',
    id: 53,
    isEditing: false,
    status: true
  },
  {
    title: 'выполненая Задача 4',
    description: 'Описание задачи4 Описание задачи4',
    id: 54,
    isEditing: false,
    status: true
  }
]
export default {
  name: 'app-todo',
  components: {
    TodoItem
  },
  data () {
    return {
      valueTitle: '',
      valueDescription: '',
      todolist: fakeData
    }
  },
  computed: {
    currentDoList () {
      return this.todolist.filter(el => el.status === false)
    },
    doneDoList () {
      return this.todolist.filter(el => el.status === true)
    },
    editingEnabled () {
      // return this.todolist.some(el => el.isEditing)
      return this.todolist.filter(el => el.isEditing).length > 0
    }
  },
  methods: {
    addTask () {
      if (this.valueTitle === '') { return };
      this.todolist.push({
        title: this.valueTitle,
        description: this.valueDescription,
        id: Math.random(),
        status: false,
        isEditing: false
      })
      this.valueTitle = ''
      this.valueDescription = ''
    },
    removeTask (task) {
      console.log('removeTask')
      const nameList = task.status ? 'ВЫПОЛНЕНЫХ' : 'ТЕКУЩИХ'
      const confirmRemove = confirm(`Удалить задачу: "${task.title}" из списка ${nameList} задач?`)

      if (confirmRemove) {
        this.todolist = this.todolist.filter(todo => todo.id !== task.id)
      }
    },
    toggleEdit (editTask) {
      console.log('toggleEdit - переключение режима редактирования')
      editTask.isEditing = !editTask.isEditing
    },
    saveEdit (editTask, newTitle, newDescription) {
      console.log('saveEdit ' + 'editTaskID = ' + editTask.id + '//' + 'newTitle = ' + newTitle + '//' + 'newDescription = ' + newDescription)
      if (newTitle !== '') {
        editTask.title = newTitle
        editTask.description = newDescription
      }
    },
    setHeightTextarea (id) {
      // todo Доделать пересчет высоты
      console.log('setHeightTextarea')
      // const elDescription = document.getElementById(`description_${index}`)
      const textarea = document.getElementById(`editDescription_${id}`)
      // textarea.setAttribute('style', 'height:' + (elDescription.scrollHeight) + 'px;overflow-y:hidden;')
      textarea.addEventListener('input', () => { textarea.style.height = (textarea.scrollHeight) + 'px' }, false)
    }
  }
}
</script>

<style lang="scss">
@import "assets/style.css";
</style>
