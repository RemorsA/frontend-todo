<template>
    <div class="d-flex pa-4 justify-center">
        <v-card class="pa-4" width="600">
            <div>
                <v-text-field
                    label="Поиск"
                    placeholder="Введите что-нибудь"
                    prepend-inner-icon="mdi-magnify"
                    hide-details
                    filled
                    clearable
                    v-model.trim="searchText"
                />
            </div>
            <div class="d-flex mt-4">
                <div class="flex-grow-1 pr-4">
                    <v-text-field
                        label="Новая задача"
                        placeholder="Введите текст задачи"
                        prepend-inner-icon="mdi-note-check-outline"
                        hide-details
                        filled
                        clearable
                        v-model.trim="newTaskText"
                    />
                </div>
                <div class="d-flex align-center">
                    <v-btn color="primary" :disabled="!newTaskText" @click="addTask">
                        Добавить
                    </v-btn>
                </div>
            </div>
            <div class="mt-4">
                <h1 class="text-center grey--text" v-if="tasks.length === 0">
                    Список задач пуст
                </h1>
                <h1 class="text-center grey--text" v-else-if="filteredTasks.length === 0">
                    Ничего не найдено
                </h1>
                <v-list v-else>
                    <template v-for="(task, index) in filteredTasks">
                        <v-list-item :key="task.id">
                            <v-list-item-content>
                                {{ index + 1 }}. {{ task.text }}
                            </v-list-item-content>
                            <v-list-item-action>
                                <v-btn color="error" icon @click="openTaskRemovingModal(task)">
                                    <v-icon>mdi-close</v-icon>
                                </v-btn>
                            </v-list-item-action>
                        </v-list-item>
                        <v-divider v-if="index < tasks.length - 1" :key="`d-${task.id}`" />
                    </template>
                </v-list>
            </div>
        </v-card>
        <v-dialog width="500" v-model="modals.taskRemoving.visible">
            <v-card>
                <v-card-title>Удаление задачи</v-card-title>
                <v-card-text>
                    Вы действительно хотите удалить задачу "{{ modals.taskRemoving.text }}"?
                </v-card-text>
                <v-card-actions>
                    <v-btn color="error" text @click="modals.taskRemoving.visible = false">
                        Отмена
                    </v-btn>
                    <v-spacer></v-spacer>
                    <v-btn color="success" text @click="removeTask">Удалить</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-snackbar color="success" bottom right v-model="toasts.taskAdding">
            Задача добавлена
            <template v-slot:action="{ attrs }">
                <v-btn icon v-bind="attrs" @click="toasts.taskAdding = false">
                    <v-icon>mdi-close</v-icon>
                </v-btn>
            </template>
        </v-snackbar>
        <v-snackbar color="success" bottom right v-model="toasts.taskRemoving">
            Задача удалена
            <template v-slot:action="{ attrs }">
                <v-btn icon v-bind="attrs" @click="toasts.taskRemoving = false">
                    <v-icon>mdi-close</v-icon>
                </v-btn>
            </template>
        </v-snackbar>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                searchText: null,
                newTaskText: null,
                tasks: [],
                toasts: {
                    taskAdding: false,
                    taskRemoving: false
                },
                modals: {
                    taskRemoving: {
                        visible: false,
                        id: 0,
                        text: ""
                    }
                }
            }
        },
        computed: {
            filteredTasks() {
                if (this.searchText) {
                    const text = this.searchText.toLowerCase()
                    return this.tasks.filter(t => t.text.toLowerCase().includes(text))
                } else {
                    return this.tasks
                }
            }
        },
        methods: {
            addTask() {
                const newTask = {
                    id: Date.now(),
                    text: this.newTaskText
                }
                this.tasks.push(newTask)
                this.updateLocalStorage()
                this.newTaskText = null
                this.toasts.taskAdding = true
            },
            removeTask() {
                this.tasks = this.tasks.filter(t => t.id !== this.modals.taskRemoving.id)
                this.updateLocalStorage()
                this.modals.taskRemoving.visible = false
                this.toasts.taskRemoving = true
            },
            openTaskRemovingModal(task) {
                this.modals.taskRemoving.id = task.id
                this.modals.taskRemoving.text = task.text
                this.modals.taskRemoving.visible = true
            },
            updateLocalStorage() {
                localStorage.setItem("tasks", JSON.stringify(this.tasks))
            }
        },
        mounted() {
            this.tasks = JSON.parse(localStorage.getItem("tasks")) ?? []
        }
    }
</script>
