<script setup>
import { ref,reactive,onMounted } from 'vue';
const todo = reactive([]);
const todo_text = ref('');
const edit_index = ref(null);
const error_msg = ref('');

function add_task(){
    if(todo_text.value==''){
        error_msg.value='Please Enter task';
        setTimeout( ()=>{
            error_msg.value='';
        },4000);
    }else{
        const task = {
            'text':todo_text.value,
            'created_date': new Date().toLocaleDateString(),
            'done': false,
            'edit': false
        }
        // console.log(edit_index.value);
        if(edit_index.value===null){
            todo.unshift(task);
        }else{
            console.log(todo[edit_index.value])
            todo[edit_index.value].text = task.text;
            todo[edit_index.value].done = task.done;
            todo[edit_index.value].edit = false;
            // alert("hii");
        }

        localStorage.setItem("tasks", JSON.stringify(todo));
        // todo.unshift(task);
        todo_text.value='';
        error_msg.value='';
        edit_index.value=null;
    }
}

function remove_error_msg(){
    if(todo_text.value.length>0){
        error_msg.value='';
    }else{
        error_msg.value='Please Enter task';
    }
}

function deleteTask(index) {
    todo.splice(index, 1);
    localStorage.setItem("tasks", JSON.stringify(todo));
}

function editTask(index){
    todo_text.value = todo[index].text;
    edit_index.value = index
    todo[index].edit = true;
}

function doneTask(index){
    if(todo[index].done==false){
        todo[index].done=true;
    }else{
        todo[index].done=false;
    }
    localStorage.setItem("tasks", JSON.stringify(todo));
}

onMounted(() => {
    let data = JSON.parse(localStorage.getItem("tasks")); 
    todo.unshift(...data)
    console.log(todo);
});
</script>

<template>
    <!-- {{ todo }} {{  edit_index }} -->
    <div class="container">
        <div class="row">
            <h2 class="text-center mt-5">TODO APP VUE 3</h2>
        </div>
        <div class="row mt-4">
            <div class="col-md-6">
                <div class="card">
                    <div class="card-header bg-primary text-light"><h4>TODO</h4></div>
                    <div class="card-body">
                        <input type="text" v-model="todo_text" placeholder="Enter Todo" class="form-control rounded-0" @blur="add_task" @keyup.enter="add_task" @keyup="remove_error_msg">
                        <span class="text-danger">{{ error_msg }}</span>      
                    </div>
                </div>
            </div>

            <div class="col-md-6"> 
                <div class="card mb-2" v-if="todo.length==0">
                    <div class="card-body todo_body">
                        <div class="todo_task">
                            <h5>No Task in Todo</h5>
                            <span class="text-muted"><em></em></span>
                        </div>
                    </div>
                </div>
                <div class="card mb-2" v-else="todo" v-for="(task,index) in todo" :key="index" :class="{'bg-success text-light':task.done, 'bg-warning':task.edit}">
                    <div class="card-body todo_body">
                        <div class="todo_task">
                            <h5>{{task.text}}</h5>
                            <span class="text-muted"><em>{{task.created_date}}</em></span>
                        </div>
                        
                        <div class="action_btn">
                            <button class="btn btn-danger btn-sm rounded-0" @click="deleteTask(index)">DELETE</button>
                            <button class="btn btn-warning btn-sm rounded-0" @click="editTask(index)">EDIT</button>
                            <button class="btn btn-success btn-sm rounded-0" v-if="!task.done" @click="doneTask(index)">DONE</button>
                            <button class="btn btn-primary btn-sm rounded-0" v-if="task.done" @click="doneTask(index)">UNDO</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .todo_body{
        position: relative;
    }
    .action_btn{
        position: absolute;
        right: 10px;
        bottom: 10px;
    }
    em{
        font-size: 13px;
    }
    .todo_task{
        word-wrap: break-word;
    }
</style>