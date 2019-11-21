<template>
    <div class="text-center task-list-container">
        <h1>{{ title }}</h1>
        <div class="alert alert-light" v-if="!taskList.length">
            Your list is empty
        </div>

        <div>
            <div class="btn-group mr-2" role="group">
                <button type="button" class="btn btn-secondary"
                        v-for="list in lists" @click="loadById(list.id)">{{ list.name }}</button>
            </div>
        </div>

        <div class="input-group mb-3 task-form" v-if="taskList.length">
            <input type="text" class="form-control" placeholder="A list name" v-model="listName">
            <div class="input-group-append">
                <button class="btn btn-success" type="button" @click="upload">Save</button>
            </div>
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
                taskList: [],
                // A name of the list
                listName: '',
                // List of all lists
                lists: []
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
            },
            upload: async function () {
                if(!this.listName) {
                    return;
                }
                let query = '?name=' + encodeURIComponent(this.listName);
                let response = await fetch(process.env.VUE_APP_API_URL+'upload'+query, {
                    method: 'POST',
                    body: JSON.stringify(this.taskList)
                });
                let result = await response.json();
                if(result.status && result.is_new) {
                    this.lists.push({id: result.id, name: result.name});
                }
            },
            loadAll: async function () {
                let response = await fetch(process.env.VUE_APP_API_URL+'list', {
                    method: 'GET'
                });
                let result = await response.json();
                this.lists = result;
            },
            loadById: async function (id) {
                let response = await fetch(process.env.VUE_APP_API_URL+'load?id='+id, {
                    method: 'GET'
                });
                let result = await response.json();
                this.listName = result.name;
                this.taskList = JSON.parse(result.data);
                this.save();
            }
        },
        created() {
            // Load data from the storage after an app is created
            let storageData = localStorage.getItem('tasks');
            if(storageData) {
                this.taskList = JSON.parse(storageData);
            }
            this.loadAll();
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