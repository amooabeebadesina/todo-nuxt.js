<template>
    <div class="container main-body">
        <todo-header/><br/>
        <v-card>
            <v-card-text>
                <v-text-field auto-grow clearable autofocus rows="1"
                              label="what do you need to do?" v-model="todo" @keyup.enter="addTodo()"
                              single-line full-width hide-details type="text" v-if="!updateMode"></v-text-field>
                <div v-if="updateMode">
                    <v-text-field auto-grow clearable autofocus rows="1"
                                  label="what do you need to do?" v-model="toUpdate"
                                  single-line full-width hide-details type="text"></v-text-field>
                    <button class="btn btn-primary" @click="updateTodo">Update</button>
                </div>
            </v-card-text>
        </v-card><br/>
        <div class="todos">
            <v-card v-for="todo in todoList" class="padding-20">
                <div class="row">
                    <div class="col-md-2 col-xs-2">
                        <span class="label label-warning" v-if="todo.completed === 0">pending</span>
                        <span class="label label-success" v-else>Completed</span>
                    </div>
                    <div class="col-md-6 col-xs-6">
                        {{todo.label}}
                    </div>
                    <div class="col-md-2 col-xs-2">
                        <button class="btn btn-primary" @click="editMode(todo)">Edit</button> &nbsp;
                        <button class="btn btn-danger" @click="deleteTodo(todo.id)">Delete</button>
                    </div>
                </div>
            </v-card>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import TodoHeader from '~/components/TodoHeader';
export default {
    components: {TodoHeader},
    data () {
        return {
            todos: [],
            api_url: 'http://localhost:8000/api/v1',
            todo: '',
            updateMode: false,
            toUpdate: '',
            toUpdateId: ''
        }
    },

    computed: {
        todoList () {
            return this.todos;
        }
    },

    created () {
      this.fetchTodos();
    },

    methods: {
        fetchTodos () {
            axios.get(`${this.api_url}/todos`)
              .then((res) => {
                  this.todos = res.data.data;
              })
              .catch((err) => {

              })
        },
        addTodo () {
            if (this.todo.length > 0) {
                const data = {
                    label: this.todo,
                    completed: false
                };
                axios.post(`${this.api_url}/todos`, data)
                    .then((res) => {
                        this.todo = '';
                        this.todos = res.data.data;
                    })
                    .catch((err) => {

                    })
            } else {
                return;
            }
        },

        deleteTodo (id) {
            axios.delete(`${this.api_url}/todos/${id}`)
                .then((res) => {
                    this.todos = res.data.data;
                })
                .catch((err) => {

                })
        },

        editMode (todo) {
            this.updateMode = true;
            this.toUpdate = todo.label;
            this.toUpdateId = todo.id;
        },

        offEditMode () {
            this.updateMode = false;
            this.toUpdate = '';
            this.toUpdateId = '';
        },

        updateTodo () {
            const data = {
                label: this.toUpdate
            }
            const id = this.toUpdateId;
            axios.put(`${this.api_url}/todos/${id}`, data)
                .then((res) => {
                    this.offEditMode();
                    this.todos = res.data.data;
                })
                .catch((err) => {

                })
        }
    }
}
</script>

<style>
    .main-body {
        padding: 20px 0px 50px;
    }
    .padding-20 {
        padding:20px;
    }
</style>
