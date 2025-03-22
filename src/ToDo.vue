<template>
    <div class="item">
      
      <div class="details">
        <form v-on:submit.prevent="addTask">
          <input type="text" v-model="taskName" @change="savetasks" placeholder="Add thing to do" required>
          <button type="submit">Add</button>
        </form>
        <div class="greetings">
            <h1 class="green">{{ msg }}</h1>
            <h3>Completed {{ filteredChecked.length }} of {{ taskList.length }} tasks.</h3>
        </div>
        
      </div>
    </div>
    <div class="content">
            <ul>
                <li v-for="task in taskList" :key="task.name" v-if="taskList.length">
                    <input type="checkbox" v-model="checked" name="task" id="task" :value="task.name">
                    <span :class="{crossed:checked.includes(task.name)}">{{ task.name }}</span>
                    <button @click="deleteTask(task)"><img src="/delete.jpg" width="24px" height="24px"></button>
                    <button @click="showAlert(task)"><img src="/edit.jpg" width="24px" height="24px"></button>
                </li>
            </ul>
        </div>
  </template>
  
  <style scoped>
  .item {
    margin-top: 2rem;
    display: flex;
    position: relative;
  }

  .greetings h3{
    color: white;
    font-size: 16px;
  }
  
  .details {
    flex: 1;
    text-align: center;
    margin-top: -40px;
  }
  .details input{
    width: 60%;
    padding: 8px;
    font-size: 18px;
  }

  .details button{
    padding: 8px;
    width: 20%;
    font-size: 18px;
    background-color: lightgrey;
  }

  .details button:hover{
    background-color: rgb(181, 213, 41);
    color: rgb(16, 48, 14);
    font-weight: bold;
  }
  
  i {
    display: flex;
    place-items: center;
    place-content: center;
    width: 32px;
    height: 32px;
  
    color: var(--color-text);
  }
  
  h3 {
    font-size: 1.2rem;
    font-weight: 500;
    margin-bottom: 0.4rem;
    color: var(--color-heading);
  }
  .content{
        width: 100%;
        padding: 20px;
        background-color: rgb(118, 199, 172);
        text-align: center;
        padding:30px;
    }

    .content li{
        list-style-type: none;
        margin-bottom: 10px;
        background-color: rgb(169, 235, 213);
        padding: 10px;
        color: black;
        font-weight: bold;
        border-radius:5px;
        text-align: left;
        font-size: 20px;
        box-shadow: 5px 5px 5px rgb(60, 194, 111);
        border-radius: 20px 0 20px 0;
    }

    .content li:hover{
        background: linear-gradient(to right,rgb(0, 255, 42), rgb(240, 228, 8),rgb(113, 246, 46), rgb(169, 235, 213));
    }
    .content li button{
        float: right;
        padding: 4px;
        margin-left: 4px;
        background-color: rgb(169, 235, 213);
        border: none;
    }

    .content ul{
        width: 60%;
        margin: 0 auto;
        padding: 0;
    }

    .content li span{
        margin-left: 5px;
        font-size: 25px;
        font-family: 'times new roman';
    }
    
    .crossed{
        text-decoration: line-through;
        color: blue;
    }

    input[type="checkbox"]{
        accent-color: blue;;
    }
  
  @media (min-width: 1024px) {
    .item {
      margin-top: 0;
      padding: 0.4rem 0 1rem calc(var(--section-gap) / 2);
    }
  
    i {
      top: calc(50% - 25px);
      left: -26px;
      position: absolute;
      border: 1px solid var(--color-border);
      background: var(--color-background);
      border-radius: 8px;
      width: 50px;
      height: 50px;
    }
  
    .item:before {
      content: ' ';
      border-left: 1px solid var(--color-border);
      position: absolute;
      left: 0;
      bottom: calc(50% + 25px);
      height: calc(50% - 25px);
    }
  
    .item:after {
      content: ' ';
      border-left: 1px solid var(--color-border);
      position: absolute;
      left: 0;
      top: calc(50% + 25px);
      height: calc(50% - 25px);
    }
  
    .item:first-of-type:before {
      display: none;
    }
  
    .item:last-of-type:after {
      display: none;
    }
  }
  </style>
  
  <script>
  import { defineComponent } from 'vue';
  import Swal from 'sweetalert2';
  import 'sweetalert2/dist/sweetalert2.min.css';
    export default defineComponent({
        data() {
            return{
                taskName: null,
                taskList: [
                    {name: 'Task 1', found: false}
                ], 
                msg: ' ',
                checked:[]
            }
        },
        computed:{
            filteredChecked(){
                return this.checked.filter(taskName => this.taskList.some(task => task.name === taskName));
            }
        },
        created(){
            this.loadtasks();
        },
        watch:{
            checked:{
                handler(){
                    this.savetasks();
                },
                deep: true
            }
        },
        methods:{
            addTask(){
                if(this.taskName){
                let task = {
                    name: this.taskName,
                    found: false
                }
                this.taskList.push(task)
                this.taskName = null
                this.savetasks();
            }
        },
        deleteTask(task){
            this.taskList = this.taskList.filter(t => t !== task);
            this.checked = this.checked.filter(taskName => taskName != task.name);
            this.savetasks();
        },
        showAlert(task){
            Swal.fire({
                title: 'Edit ToDo!',
                text: 'Please edit task name',
                input: 'text',
                inputValue: task.name,
                icon: 'success',
                confirmButtonText: 'Save',

                preConfirm: (newTaskName) => {
                    const index = this.taskList.indexOf(task);
                    if(index !== -1){
                        this.taskList[index].name = newTaskName;
                        this.savetasks();
                    }
                },
                showCancelButton: true,
            })
        },
        savetasks(){
            localStorage.setItem("tasks", JSON.stringify(this.taskList));
            localStorage.setItem("checked", JSON.stringify(this.checked));
        },
        loadtasks(){
            const savedtasks = localStorage.getItem("tasks");
            const savedChecked = localStorage.getItem("checked");
            if(savedtasks){
                try{
                    this.taskList = JSON.parse(savedtasks);
                }catch(e){
                    console.error("Error loading tasks", e);
                }
            }
            if(savedChecked){
                try{
                    this.checked = JSON.parse(savedChecked);
                }catch(e){
                    console.error("Error loading checked tasks", e);
                }
            }
        }
    }
    });
    
  </script>