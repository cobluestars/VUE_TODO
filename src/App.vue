<template>
  <div class="container">
    <!-- <h4>count: {{ count }}</h4>
    <h4>doubleCount: {{ doubleCount }}</h4>
    <h4>doubleCount: {{ doubleCount }}</h4>
    <h4>doublecountFunction {{ doubleCountFunc() }}</h4>
    <h4>doublecountFunction {{ doubleCountFunc() }}</h4>
    <button @click="count++">Add One</button> -->
    <h1 class="text-center">To-Do List</h1>
    <input
        class="form-control"
        type="text"
        v-model="searchText"
        placeholder="검색"
    >
    <SimpleForm @add-todo="addTodo"/>
        <div v-show="hasError" class="text-danger">
        내용은 무조건 입력하셔야 합니다.
        </div>
        <div v-if="!filteredTodoList.length" class="text-warning">
        추가된 내용이 없습니다.
        </div>
        <TodoList 
            :todoList="filteredTodoList"
            @toggle-todo="toggleTodo"
            @todo-delete="onDelete"
        />
  </div>
  <!-- ★ 자식에서 부모로 이벤트나 값 전달 ☞ emit , 부모에서 자식으로 이벤트나 값 전달 ☞ props ★ -->
</template>

<script>
import { ref, computed } from "vue";
import SimpleForm from "./components/SimpleForm.vue"
import TodoList from "./components/TodoList.vue"

  export default {
    components: {
      SimpleForm,
      TodoList,
    },

    setup() {
      const todo = ref("");
      const todoList = ref([]);
      const hasError = ref(false);
      const completedStyle = {
        textDecoration: "line-through",
        color: "gray",
      }
      const searchText = ref("")
      const filteredTodoList = computed(() => {
        if (searchText.value) {
          return todoList.value.filter((loop) => {
            return loop.content.includes(searchText.value);
          })
        }
        return todoList.value;
      })

      function addTodo(todos) {
        console.log(todos);
        todoList.value.push(todos);
      }

      function onSubmit() {
        if (todo.value === "") {
          hasError.value = true;
        } else {
        console.log(todo.value);
        todoList.value.push({
          id: Date.now(),
          content: todo.value,
          completed: false,
        })
        hasError.value = false;
        todo.value = "";
      }
    }

    function toggleTodo(index) {
      console.log(index);
    }

    function onDelete(index) {
      todoList.value.splice(index, 1);
    }

    // const count = ref(1);
    // const doubleCount = computed(() => {
    //   console.log("computed active!");
    //   return count.value * 2;
    // });

    // function doubleCountFunc() {
    //   console.log("function active!");
    //   return count.value * 2;
    // }

      return { addTodo, toggleTodo, todo, todoList, onSubmit, completedStyle, hasError, onDelete, searchText, filteredTodoList };
    },
  };
</script>

<style lang="scss" scoped>

</style>