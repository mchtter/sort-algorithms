<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <table class="table align-middle">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Title</th>
          <th scope="col">Assignee</th>
          <th scope="col">Status</th>
          <th scope="col">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(todo, index) in todos[0]" :key="index">
          <th scope="row">{{ todo.id }}</th>
          <td>{{ todo.title }}</td>
          <td>{{ todo.userId }}</td>
          <td>{{ todo.completed }}</td>
          <td>
            <button type="button" class="btn btn-primary btn-sm px-3 m-2">
              <i class="fas fa-pen"></i>
            </button>
            <button type="button" class="btn btn-danger btn-sm px-3 m-2">
              <i class="fas fa-times"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      todos: [],
      users: [],
    };
  },
  async created() {
    await this.getData();
    // console.log(this.todos);
  },
  methods: {
    getData() {
      axios
        .get("https://jsonplaceholder.typicode.com/todos")
        .then((response) => {
          this.todos.push(response.data);
        });
    },
  },
  components: {},
  computed: {
    isCompleted: () => {
      todos.forEach((element) => {
        console.log(element);
      });
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
