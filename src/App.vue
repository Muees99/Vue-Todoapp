<template>
  <div class="parent-div">
    <Todo-Header>
      <div class="flex">
        <input 
          type="text" 
          name="" 
          placeholder="New todo here . . ."
          class="my-input"
          v-model="new_todo_desc"
        >
        <button class="btn-add" @click="add_todo()" v-if="is_add">Add</button>
        <button class="btn-add" @click="update_todo" v-else>Update</button>
      </div>
      <p class="invalid-input" v-if="is_empty_field"> Enter a Task!</p>
    </Todo-Header>
    <Todo-Main>
      <p class="no-todos" v-if="todos.length === 0">No todos available!</p>
      <div class="todos-main-container" v-else>
        <div 
          class="todos-container" 
          v-for="(todo, index) in todos"
          :key="index"
        >
          <div class="todos-text-container">
            <p class="todo-date">{{todo.date}}</p>
            <p class="todo-desc">{{todo.desc}}</p>
          </div>
          <div class="common-btn-container">
            <button class="btn-common" @click="edit_todo(index, todo.date, todo.desc)"><svg class="common-btn-svg" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"></path></svg></button>
            <button class="btn-common" @click="delete_todo(index)"><svg class="common-btn-svg" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg></button>
          </div>
        </div>
      </div>
    </Todo-Main>
  </div>
</template>

<script>
  import TodoHeader from './components/TodoHeader.vue';
  import TodoMain from './components/TodoMain.vue';
  import {ref, reactive} from 'vue';
  import swal from 'sweetalert';
  export default { 
    components : {
      TodoHeader,
      TodoMain,
    },
    data(){
      return {
        todos: [],
        edit_todo_index: 0,
        edit_todo_date: '',
        is_empty_field:  false,
        new_todo_desc: '',
        is_add: true
      }
    },
    mounted(){
      let storedData = localStorage.getItem('myTodoList')
     
      if(storedData.length){
        this.todos = JSON.parse(storedData)
      }else{
        this.todos = []
      }
    
     
    },
    methods:{
       add_todo () {
        if (this.new_todo_desc === '') {
          this.is_empty_field = true;
        }
        else {
          this.is_empty_field = false;
          this.todos = [...this.todos,{
              date : new Date().toDateString(),
              desc : this.new_todo_desc
            }]
            this.new_todo_desc = '';
            this.saveToLocalStorage(this.todos)
         
          setTimeout(() => {
            swal({
            text: "Todo item added successfully!",
            icon: "success",
            button: "Okay",
          });
          }, 500)
        }
      },
      edit_todo(ind, date, desc){
        this.is_add = false;
        this.edit_todo_index = ind;
        this.edit_todo_date = date;
        this.new_todo_desc = desc;
      },
      update_todo(){
        if (this.new_todo_desc === '') {
          this.is_empty_field = true;
        }
        else {
          this.todos[this.edit_todo_index] = {
            date : this.edit_todo_date,
            desc : this.new_todo_desc,
          }
          this.is_add = true;
          this.new_todo_desc = '';
          this.saveToLocalStorage(this.todos)
          swal({
            icon: "success",
            text: "Todo updated successfully!",
          });
         
        }
      },
      delete_todo(index){
        swal({
          title: "Delete todo",
          text: "Are you sure on deleting this todo item?",
          icon: "warning",
          buttons: true,
          dangerMode: true,
        })
        .then((willDelete) => {
          if (willDelete) {
            this.todos.splice(index, 1);
            this.saveToLocalStorage(this.todos)
            swal("Todo item has been deleted", {
              icon: "success",
            });
            this.new_todo_desc = '';
            this.is_empty_field = false;
          }
        });
      },
      saveToLocalStorage(todo){
        localStorage.setItem('myTodoList', JSON.stringify(todo));
      }
    }
  }

   
</script>
