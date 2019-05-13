<template>
    <div class="hello">
        <div id="todo-list-example" class="container">
            <div class="row">
                <div class="col-md-6 mx-auto">
                    <h1 class="text-center">TODO List App</h1>
                    <form v-on:submit.prevent="addNewTask()">
                        <label for="tasknameinput">Task Name</label>
                        <input v-model="task_name" type="text" id="tasknameinput" class="form-control" placeholder="Add New Task">
                        <button v-if="this.isEdit == false" type="submit" class="btn btn-success btn-block mt-3">Submit</button>
                        <button v-else v-on:click="updateTask()" type="button" class="btn btn-primary btn-block mt-3">Update</button>
                    </form>

                    <table class="table">
                        <tr v-for="(todo) in todos" v-bind:key="todo.id" v-bind:title="todo.task_name">
                            <td class="text-left">{{ todo.task_name }}</td>
                            <td class="text-right">
                                <button class="btn btn-info" v-on:click="editTask(todo.task_name, todo.id)">Edit</button>
                                <button class="btn btn-danger" v-on:click="deleteTask(todo.id)">Delete</button>
                            </td>
                        </tr>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    data() {
        return {
            todos: [],
            id: '',
            task_name: '',
            isEdit: false
        }
    },
    mounted() {
        this.getTasks()
    },
    methods: {
        getTasks() {
            axios.get('/api/tasks').then(
                result => {
                    this.todos = result.data;
                },
                error => {
                    console.log(error);
                }
            );
        },
        addNewTask() {
            axios.post('/api/task', { task_name: this.task_name})
            .then((res) => {
                    this.task_name = '';
                    this.getTasks();
            })
            .catch((err) => {
                console.log(err);
            });
        },
        editTask(title, id) {
            this.id = id;
            this.task_name = title;
            this.isEdit = true;
        },
        updateTask() {
            axios.put(`/api/task/${ this.id }`, { task_name: this.task_name })
            .then((res) => {
                this.task_name = '';
                this.isEdit = false;
                this.getTasks();
                console.log(res);
            })
            .catch((err) => {
                console.log(err);
            })
        },
        deleteTask(id) {
            axios.delete(`/api/task/${ id }`)
            .then((res) => {
                this.task_name = '';
                this.getTasks();
                console.log(res);
            })
            .catch((err) => {
                console.log(err);
            })
        }
    }
}
</script>
