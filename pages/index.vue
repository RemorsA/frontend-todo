<template>
  <section>
    <v-card class="container" elevation="2" outlined>
      <div class="container-search">
        <v-text-field
          label="Поиск"
          prepend-inner-icon="mdi-magnify"
          v-model="search"
          filled
          dense
          clearable
        />
      </div>
      <div class="container-create-tasks">
        <v-text-field
          label="Задача"
          prepend-inner-icon="mdi-book"
          v-model="title"
          filled
          dense
          clearable
        />

        <div class="container-btn">
          <v-btn color="green" elevation="2" :disabled="title === ''" @click="addTask">
            Добавить
          </v-btn>
        </div>
      </div>
      <div class="container-tasks" v-if="searchTasks.length === 0">Ничего не найдено</div>
      <div class="container-tasks" v-if="tasks.length === 0">Список задач пуст</div>
      <div class="task" v-else v-for="(task, index) in searchTasks" :key="task.id">
        {{ index + 1 }}. {{ task.title }}

        <v-spacer></v-spacer>
        <v-btn @click="modalWindow(task)" icon>
          <v-icon> mdi-delete </v-icon>
        </v-btn>
      </div>
    </v-card>
    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title style="justify-content: center">
          Вы действительно хотите удалить запись
        </v-card-title>
        <v-card-title style="justify-content: center"> "{{ title2 }}" ?</v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="red" text @click="dialog = false" style="margin-right: 300px">
            Отмена
          </v-btn>
          <v-btn color="green" text @click="deleteTask(id2)"> Удалить </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar v-model="snackbarAdd" color="green">
      {{ textAdd }}
      <v-btn color="white" text @click="snackbarAdd = false" style="margin-left: 200px">
        Закрыть
      </v-btn>
    </v-snackbar>
    <v-snackbar v-model="snackbarDelete" color="green">
      {{ textDelete }}
      <v-btn color="white" text @click="snackbarDelete = false" style="margin-left: 200px">
        Закрыть
      </v-btn>
    </v-snackbar>
  </section>
</template>

<script>
  export default {
    data() {
      return {
        id2: 0,
        title2: '',
        tasks: [],
        title: '',
        search: '',
        dialog: false,
        snackbarAdd: false,
        snackbarDelete: false,
        textDelete: `Запись удалена!`,
        textAdd: `Запись добавлена`
      }
    },
    methods: {
      addTask() {
        const newTasks = {
          id: Date.now(),
          title: this.title
        }
        console.log(newTasks)
        this.tasks.push(newTasks)
        this.localStorageObj()
        this.title = ''
        this.snackbarAdd = true
      },
      modalWindow(task) {
        this.id2 = task.id
        this.title2 = task.title
        this.dialog = true
      },
      deleteTask(id) {
        this.tasks = this.tasks.filter(t => t.id !== id)
        this.dialog = false
        this.snackbarDelete = true
        this.localStorageObj()
      },
      localStorageObj() {
        const parsed = JSON.stringify(this.tasks)
        localStorage.setItem('tasks', parsed)
      }
    },
    computed: {
      searchTasks() {
        if (this.search === null) {
          return this.tasks
        } else {
          return this.tasks.filter(e => {
            return e.title.toUpperCase().includes(this.search.toUpperCase())
          })
        }
      }
    },
    mounted() {
      if (localStorage.getItem('tasks')) {
        try {
          this.tasks = JSON.parse(localStorage.getItem('tasks'))
        } catch (e) {
          localStorage.removeItem('tasks')
        }
      }
    }
  }
</script>

<style>
  * {
    margin: 0%;
    padding: 0%;
    box-sizing: border-box;
  }

  .container {
    margin-top: 20px;
    width: 500px;
    height: auto;
    background-color: #e1ebeb;
    border: 1px solid #a4abab;
  }

  .container-create-tasks {
    display: flex;
  }

  .container-btn {
    padding: 10px;
  }

  .container-tasks {
    text-align: center;
  }

  .task {
    display: flex;
    align-items: center;
  }
</style>
