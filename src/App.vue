<template>
  <div id="app">
      <section class="hero is-primary">
          <div class="hero-body">
              <div class="container">
                  <h1 class="title">
                      Too-Da-Loo | ToDo
                  </h1>
              </div>
          </div>
      </section>

      <div class="container">
          <div class="field is-grouped is-grouped-centered">
              <p class="control">
                  <input class="input" type="text" placeholder="Name" min="1" maxlength="30" v-model="taskInput.name">
              </p>
              <p class="control is-expanded">
                  <input class="input" type="text" placeholder="Description" maxlength="60" v-model="taskInput.description">
              </p>
              <p class="control">
                  <button class="button is-info" v-on:click="createTask">
                      Create
                  </button>
                  <button class="button is-warning" v-on:click="clearCreateForm">
                      Clear
                  </button>
              </p>
          </div>

          <div class="field is-grouped is-grouped-centered">
              <p class="control">
                  <button class="button is-danger" v-on:click="deleteAll">
                      Delete All
                  </button>
              </p>
          </div>

          <hr/>

          <div class="columns">
              <div class="column still-todo is-one-third">
                  <h1>ToDo</h1>
                  <div v-for="todoTask in this.todoTasks">
                      <div class="card card-todo">
                          <header class="card-header">
                              <p class="card-header-title">
                                  {{ todoTask.name }}
                              </p>
                              <a href="#" class="card-header-icon" aria-label="more options">
                                  <span class="icon">
                                    <i class="fas fa-angle-down" aria-hidden="true"></i>
                                  </span>
                              </a>
                          </header>
                          <div class="card-content">
                              <div class="content">
                                  {{ todoTask.description }}
                                  <hr/>
                                  <p>Created</p>
                                  <time>{{ todoTask.created }}</time>
                              </div>
                          </div>
                          <footer class="card-footer">
                              <a class="card-footer-item button is-success" @click="updateTaskStatus(todoTask.id, 'progress')">In Progress >></a>
                          </footer>
                      </div>
                  </div>
              </div>
              <div class="column all-todo is-one-third">
                  <h1>In Progress</h1>
                  <div v-for="progressTask in progressTasks">
                      <div class="card card-in-progress">
                          <header class="card-header">
                              <p class="card-header-title">
                                  {{ progressTask.name }}
                              </p>
                              <a href="#" class="card-header-icon" aria-label="more options">
                                  <span class="icon">
                                    <i class="fas fa-angle-down" aria-hidden="true"></i>
                                  </span>
                              </a>
                          </header>
                          <div class="card-content">
                              <div class="content">
                                  {{ progressTask.description }}
                                  <hr/>
                                  <p>Created</p>
                                  <time>{{ progressTask.created }}</time>
                              </div>
                          </div>
                          <footer class="card-footer">
                              <a href="#" class="card-footer-item button is-danger" @click="updateTaskStatus(progressTask.id, 'todo')"><< ToDo</a>
                              <a href="#" class="card-footer-item button is-success" @click="updateTaskStatus(progressTask.id, 'completed')">Completed >></a>
                          </footer>
                      </div>
                  </div>
              </div>
              <div class="column completed-todo is-one-third">
                  <h1>Completed</h1>
                  <div v-for="completedTask in completedTasks">
                      <div class="card card-completed">
                          <header class="card-header">
                              <p class="card-header-title has-text-centered">
                                  {{ completedTask.name }}
                              </p>
                              <a href="#" class="card-header-icon" aria-label="more options">
                                  <span class="icon">
                                    <i class="fas fa-angle-down" aria-hidden="true"></i>
                                  </span>
                              </a>
                          </header>
                          <div class="card-content">
                              <div class="content">
                                  {{ completedTask.description }}
                                  <hr/>
                                  <p>Created</p>
                                  <time>{{ completedTask.created }}</time>
                              </div>
                          </div>
                          <footer class="card-footer">
                              <a href="#" class="card-footer-item button is-danger" @click="updateTaskStatus(completedTask.id, 'progress')"><< In Progress</a>
                          </footer>
                      </div>
                  </div>
              </div>
          </div>
      </div>
  </div>
</template>

<script>
    import ls from 'local-storage';

    export default {

        name: 'app',

        created() {

            this.buildBoard();

        },

        methods: {

            buildBoard() {

                const tasks = ls.get('tasks');

                if (tasks !== null) {
                    this.tasks = tasks;
                } else {
                    ls.set('tasks', []);
                }

                this.resetColumns();

                for (let index = 0; index < tasks.length; ++index) {

                    this.task = tasks[index];

                    if (this.task.status == 'todo') {

                        this.todoTasks.push(this.task);

                    }

                    if (this.task.status == 'progress') {

                        this.progressTasks.push(this.task);

                    }

                    if (this.task.status == 'completed') {

                        this.completedTasks.push(this.task);

                    }

                }

                this.clearTask();

            },

            resetColumns() {

                this.todoTasks = [];
                this.progressTasks = [];
                this.completedTasks = [];

            },

            isValidTask() {

                let validTask = true;

                if (!this.taskInput.name) {

                    validTask = false;
                    this.validateTask.name = false;

                }

                if (!this.taskInput.description) {

                    validTask = false;
                    this.validateTask.description = false;

                }

                return validTask;

            },

            createTask() {

                if (!this.isValidTask()) {

                    this.clearCreateForm();
                    return;

                }

                let task = {

                    id: this.tasks.length,
                    name: this.taskInput.name,
                    description: this.taskInput.description,
                    status: 'todo',
                    created: Date()

                };

                this.pushTask(task);

                this.clearTask();

                this.clearCreateForm();

            },

            pushTask(task) {

                this.tasks.push(task);
                this.todoTasks.push(task);

                let tasks = ls.get('tasks');

                tasks.push(task);

                ls.set('tasks', tasks);

            },

            clearCreateForm() {

                this.taskInput.name = '';
                this.taskInput.description = '';

            },

            clearTask() {

                this.taskInput.name = '';
                this.taskInput.description = '';
                this.taskInput.status = 'todo';

            },

            updateTaskStatus(taskId, status) {

                let tasks = ls.get('tasks');

                tasks[taskId]['status'] = status;

                ls.set('tasks', tasks);

                this.buildBoard();

            },

            deleteAll() {

                this.tasks = [];
                ls.clear();
                ls.set('tasks', this.tasks);
                this.buildBoard();

            }

        },

        data () {

            return {

                validateTask: {
                    name: true,
                    description: true
                },
                taskInput: {
                    name: '',
                    description: ''
                },
                task: {
                    name: '',
                    description: '',
                    status: '',
                    created: '',
                    updated: ''
                },
                tasks: [],
                todoTasks: [],
                progressTasks: [],
                completedTasks: []

            }

        }

    }
</script>

<style>
    @import "../node_modules/bulma/css/bulma.min.css";

    #app {
      font-family: 'Avenir', Helvetica, Arial, sans-serif;
      text-align: center;
    }

    .field {
        margin: 10px;
    }

    .card {
        margin-top: 30px;
    }
</style>
