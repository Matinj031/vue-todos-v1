<script setup>
import { ref,watch,computed } from "vue";
import TodoCreator from "../components/TodoCreator.vue";
import Todoitem from "../components/Todoitem.vue";
import { Icon } from "@iconify/vue";
import { uid } from "uid";
const todoLists = ref([]);


watch(todoLists,()=> {
  setTodoListLocalStorage()
},{
  deep:true
})


const todoCompleted = computed(()=> {
  return todoLists.value.every((todo)=> todo.isCompleted)
})


const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"))
  if(savedTodoList){
    todoLists.value = savedTodoList
  }
};

fetchTodoList()

const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoLists.value));
};

const createTodo = (todo) => {
  todoLists.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null,
  });
};

const toggleTodoComplete = (todoPos) => {
  todoLists.value[todoPos].isCompleted = !todoLists.value[todoPos].isCompleted;
};

const toggleEditTodo = (todoPos) => {
  todoLists.value[todoPos].isEditing = !todoLists.value[todoPos].isEditing;
};

const updateTodo = (todoVal, todoPos) => {
  todoLists.value[todoPos].todo = todoVal;
};

const deleteTodo = (todoUid) => {
  todoLists.value = todoLists.value.filter((todo) => todo.id !== todoUid);
};
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoLists.length > 0">
      <Todoitem
        v-for="(todo, index) in todoLists"
        :todo="todo"
        :index="index"
        :key="index"
        @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo"
      />
    </ul>
    <p class="todos-msg" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todoCompleted && todoLists.length > 0 " class="todos-msg">
      <Icon icon="openmoji:party-popper" width="90" height="90" />
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>

<style scoped lang="scss">
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
