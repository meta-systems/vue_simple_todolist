<template>
    <div class="text-center task-list-container">
        <h1>{{ title }}</h1>
        <div class="alert alert-light" v-if="!taskList.length">
            Your list is empty
        </div>

        <ul class="list-group">
            <li class="list-group-item" v-for="(task, index) in taskList">
                {{ task }}
                <button type="button" class="close" @click="remove(index)">
                    <span aria-hidden="true">&times;</span>
                </button>
            </li>
        </ul>

        <div class="input-group mb-3 task-form">
            <input type="text" class="form-control" placeholder="Task to do" v-model="formText">
            <div class="input-group-append">
                <button class="btn btn-primary" type="button" @click="add">Add</button>
            </div>
        </div>
    </div>
</template>

<script>
    // TODO: add a "Clear all" button
    // TODO: show an amount of added tasks
    export default {
        data: function () {
            return {
                // An app title
                title: 'To do list',
                // Text from user form input
                formText: '',
                // An array containing a task list
                taskList: []
            }
        },
        methods: {
            add: function () {
                // Validate data
                if(!this.formText) {
                    return;
                }
                // Add data
                this.taskList.push(this.formText);
                // Clear the form
                this.formText = null;
                // Save data to the storage
                this.save();
            },
            save: function () {
                localStorage.setItem('tasks', JSON.stringify(this.taskList));
            },
            remove: function (index) {
                this.taskList.splice(index, 1);
                this.save();
            }
        },
        created() {
            // Load data from the storage after an app is created
            let storageData = localStorage.getItem('tasks');
            if(storageData) {
                this.taskList = JSON.parse(storageData);
            }
        }
    }
</script>

<style>
    .task-list-container {
        max-width: 500px;
        margin: 0 auto;
    }
    .task-form {
        padding: 20px 0;
    }
</style>