<template>
 <div>
     <input type="text" class="todo-input" placeholder="What needs to be done" v-model="newTodo" @keyup.enter="addTodo">

    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">

     <!-- etherate the ToDo base on 3 condition -->
     <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
         <div class="todo-item-left">
             <input type="checkbox" v-model="todo.completed">
             <div v-if="!todo.editing" @dblclick="editTodo(todo)" 
              class="todo-item-label" :class="{ completed :todo.completed }"> {{ todo.title }} </div>
             <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)" v-focus>
         </div>
         <div class="remove-item" @click="removeTodo(index)">
            &times;
         </div>
     </div>
    </transition-group>
     <!-- step for check all and the lefted item *** change check = "checkAllTodos" is for  all list at once-->
         <div class="extra-container">
             <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">Check All</label></div>
             <div>{{ remaining }} item left</div>
         </div>
         <div class="extra-container">
             <div>
                 <!-- condition class base on the data property which is filter  -->
                <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
                <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Acvtive</button>
                <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
             </div>
             <div>
                 <transition name="fade">
                     <button v-if="showClearCompeletedButton" @click="clearCompeleted"> Clear All</button>
                 </transition>
             </div>
         </div>
 </div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos:[
          {
              'id': 1,
              'title': 'finish Vue Screencast',
              'completed': false,
              'editing': false,
          },
          {
              'id': 2,
              'title': 'Distroy the world',
              'completed': false,
              'editing': false,
          },
      ]
    }
  },
  computed: {   //grabing the data which is not checked by the user
      remaining() {
          return this.todos.filter(todo => !todo.completed).length  
      },
      anyRemaining(){//to checking once you check all the list it will auto show all has been check
          return this.remaining != 0
      },
      //activating the 3 function in the system 
      todosFiltered(){
          if(this.filter =='all'){
              return this.todos
          } else if (this.filter == 'active'){
              return this.todos.filter(todo => !todo.completed)
          }else if (this.filter == 'completed'){
              return this.todos.filter(todo => todo.completed)
          }
          return this.todos
      },
      showClearCompeletedButton(){
          return this.todos.filter(todo => todo.completed).length > 0
      },
  },
  
  directives:{
      focus   :{
          inserted: function(el){
              el.focus()
          }
      }
  },
  

  methods: {
      addTodo(){
          if(this.newTodo.trim().length == 0){
              return
          }
          this.todos.push({
              id: this.idForTodo,
              title: this.newTodo,
              completed: false,
          })
          this.newTodo = ''
          this.idForTodo++
      },
      editTodo(todo){
          this.beforeEditCache = todo.title
          todo.editing = true
      },
      doneEdit(todo){
          if(todo.title.trim() == 0){
              todo.title = this.beforeEditCache
          }
          todo.editing = false
      },
      cancelEdit(todo){
          todo.title = this.beforeEditCache
          todo.editing = false
      },
      removeTodo(index){  
          this.todos.splice(index, 1)
      },
      checkAllTodos(){
          this.todos.forEach((todo) => todo.completed = event.target.checked)
      },
       clearCompeleted(){// clearing the list which its been completed ** showd be at end as it is a last activity 
          this.todos = this.todos.filter(todo => !todo.completed)
      },
  }
}
</script>


<style lang="scss">
 @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
.todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus{
        outline: 0;
    }
}
    .todo-item {
        margin-bottom: 12px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .remove-item {
        cursor: pointer;
        margin-left: 14px;
        &:hover{
            color: rgb(0, 0, 0);
        }
    }
     .todo-item-left { // later
    display: flex;
    align-items: center;
  }
  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }

    .todo-item-edit {
        font-size: 24px;
        color: #2c3e50;
        margin-left: 12px;
        width: 100%;
        padding: 10px;
        border: 1px solid #ccc;
        font-family: 'Avenir', Arial, Helvetica, sans-serif;
        
        &:focus{
            outline: none;
        }
    }
    .completed {
        text-decoration: line-through;
        color: gray;
    }
    //////////////////////////////////@at-root//////////////////////
     .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }
  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    &:hover {
      background: lightgreen;
    }
    &:focus {
      outline: none;
    }
  }
  .active {
    background: lightgreen;
  }
   // CSS Transitions
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
