<template>
  <router-view />
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
        @keyup.enter="searchTodo"
    >
    <hr />
    <SimpleForm @add-todo="addTodo"/>
        <div class="text-danger" v-if="axiosErrorMessage.length != 0">
            {{ axiosErrorMessage }}          
        </div>
        <div v-show="hasError" class="text-danger">
        내용은 무조건 입력하셔야 합니다.
        </div>
        <div v-if="!todoList.length" class="text-warning">
        추가된 내용이 없습니다.
        </div>
        <TodoList 
            :todoList="todoList"
            @toggle-todo="toggleTodo"
            @todo-delete="onDelete"
        />
        <hr />
        <nav aria-label="Page navigation example">
          <ul class="pagination">
            <li v-if="currentPage !== 1" class="page-item">
              <a style="cursor: pointer" class="page-link" @click="getTodoList(currentPage - 1)">Previous</a>
            </li>
            <li
              v-for="loop in numberOfPages"
              :key="loop"
              class="page-item"
              :class="currentPage === loop ? 'active' : ''"
            >
                <a style="cursor: pointer" class="page-link" @click="getTodoList(loop)">{{ loop }}</a>
            </li>
            <li v-if="numberOfPages !== currentPage" class="page-item">
                <a style="cursor: pointer" class="page-link" @click="getTodoList(currentPage + 1)">Next</a>
            </li>
          </ul>
      </nav>
    </div>
  <!-- ★ 자식에서 부모로 이벤트나 값 전달 ☞ emit , 부모에서 자식으로 이벤트나 값 전달 ☞ props ★ -->
</template>

<script>
import { ref, computed, watch } from "vue";
import SimpleForm from "./components/SimpleForm.vue";
import TodoList from "./components/TodoList.vue";
import axios from "axios";

  export default {
    components: {
      SimpleForm,
      TodoList,
    },

    setup() {
      const todo = ref("");
      const todoList = ref([]);
      const axiosErrorMessage = ref("");
      const hasError = ref(false);
      const numberOfTodoList = ref(0);
      const currentPage = ref(1);
      const limit = 5;
      const searchText = ref("");

      // const test = reactive({
      //   testKey: 1,
      //   testKKK: 3,
      // })

      // watch(() => [currentPage, numberOfTodoList], (current, prev) => {
      //   console.log("watch()가 동작했나요?");
      //   console.log("현재 값: ", current);
      //   console.log("이전 값: ", prev);
      // })

      let timeout = null;

      const searchTodo = () => {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
          getTodoList(1);
        }, 2000);
      }

      watch(searchText, () => {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
          getTodoList(1);
        }, 2000);
      })

      // test.testKey = 2;
      // test.testKKK = 5;

      const numberOfPages = computed(() => {
        return Math.ceil(numberOfTodoList.value/limit);
      })

      const completedStyle = {
        textDecoration: "line-through",
        color: "gray",
      }

      // const filteredTodoList = computed(() => {
      //   if (searchText.value) {
      //     return todoList.value.filter((loop) => {
      //       return loop.content.includes(searchText.value);
      //     })
      //   }
      //   return todoList.value;
      // })

      async function getTodoList(pageNumber = currentPage.value) {
        currentPage.value = pageNumber;
        axiosErrorMessage.value = "";
        try {
          const res = await axios.get(
            `http://localhost:3000/todolist?_sort=id&_order=desc&content_like=${searchText.value}&_page=${pageNumber}&_limit=${limit}`
            );
          numberOfTodoList.value = res.headers['x-total-count'];
          todoList.value = res.data;
        } catch (err) {
          console.log(err);
          axiosErrorMessage.value = "에러났지롱";
        }
      }

      getTodoList();

      async function addTodo(todos) {
        axiosErrorMessage.value = "";
        try {
            await axios.post("http://localhost:3000/todolist", {
            content: todos.content,
            completed: todos.completed,
          });
          getTodoList(1);
        } catch (err) {
          axiosErrorMessage.value = "에러났지롱";
          console.log(err);
        }
      }

      // function addTodo(todos) {
      //   axiosErrorMessage.value = "";
      //   console.log("Start");
      //   axios.post("http://localhost:3000/todolist", {
      //     content: todos.content,
      //     completed: todos.completed,
      //   }).then((res) => {
      //     console.log(res);
      //     todoList.value.push(todos);
      //     console.log(todoList);
      //   }).catch((err) => {
      //     axiosErrorMessage.value = "에러났지롱"
      //     console.log(err);
      //   });
      //   console.log("End");
      // }

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

    async function toggleTodo(index) {
      axiosErrorMessage.value = "";
      const id = todoList.value[index].id;
      try {
        await axios.patch("http://localhost:3000/todolist/" + id, {
          completed: !todoList.value[index].completed,
        })
      } catch (err) {
        axiosErrorMessage.value = "네트워크 에러났지롱";
        console.log(err);
      }
      todoList.value[index].completed = !todoList.value[index].completed;
    }

    async function onDelete(index) {
      axiosErrorMessage.value = "";
      const id = todoList.value[index].id;
      try {
        await axios.delete("http://localhost:3000/todoList/" + id);
        getTodoList(1);
        // console.log(res);
        // todoList.value.splice(index, 1);
      } catch (err) {
        axiosErrorMessage.value = "에러났지롱";
        console.log(err);
      }
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

      return { addTodo, toggleTodo, todo, todoList, onSubmit, completedStyle, hasError, onDelete, searchText,
          // filteredTodoList,
          axiosErrorMessage, numberOfPages, currentPage, getTodoList, searchTodo, };
    },
  };
</script>

<style lang="scss" scoped>

</style>