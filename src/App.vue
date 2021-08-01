<template>
  <div id="app">
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
        <tr v-for="(todo, index) in todos.slice(0, 10)" :key="index">
          <th scope="row">{{ todo.id }}</th>
          <td>{{ todo.title }}</td>
          <td>{{ todo.userId }}</td>
          <td>{{ todo.completed }}</td>
          <td>
            <button
              @click="editTodo(todo.id)"
              type="button"
              class="btn btn-primary btn-sm px-3 m-2"
              data-mdb-toggle="modal"
              data-mdb-target="#editModal"
            >
              <i class="fas fa-pen"></i>
            </button>
            <button
              @click="deleteTodo(todo.id)"
              type="button"
              class="btn btn-danger btn-sm px-3 m-2"
            >
              <i class="fas fa-times"></i>
            </button>
          </td>

          <!-- Modal -->
          <div
            class="modal fade"
            id="editModal"
            tabindex="-1"
            aria-labelledby="exampleModalLabel"
            aria-hidden="true"
          >
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title" id="exampleModalLabel">Todo Edit</h5>
                  <button
                    type="button"
                    class="btn-close"
                    data-mdb-dismiss="modal"
                    aria-label="Close"
                  ></button>
                </div>
                <div class="modal-body">
                  <div class="row">
                    <div class="container">
                      <div class="form-outline">
                        <input type="text" id="newTitle" class="form-control" />
                        <label class="form-label" for="newTitle"
                          >Input New Title
                        </label>
                      </div>
                      <hr />
                      <!-- Default checkbox -->
                      <div class="form-check">
                        <input
                          class="form-check-input"
                          type="checkbox"
                          value=""
                          id="flexCheckDefault"
                        />
                        <label class="form-check-label" for="flexCheckDefault">
                          Completed
                        </label>
                      </div>
                    </div>
                  </div>
                </div>

                <div class="modal-footer">
                  <button
                    type="button"
                    class="btn btn-danger"
                    data-mdb-dismiss="modal"
                  >
                    Close
                  </button>
                  <button
                    @click="confirmEdit(this.newTitle)"
                    type="button"
                    class="btn btn-success"
                  >
                    Save changes
                  </button>
                </div>
              </div>
            </div>
          </div>
        </tr>
      </tbody>
    </table>
    <div class="d-flex justify-content-center">
      <nav>
        <ul class="pagination">
          <li class="page-item disabled">
            <span class="page-link">Previous</span>
          </li>

          <li class="page-item">
            <a class="page-link" href="#">1</a>
          </li>

          <li class="page-item active">
            <a class="page-link"> 2 </a>
          </li>

          <li class="page-item">
            <a class="page-link" href="#">3</a>
          </li>

          <li class="page-item">
            <a class="page-link" href="#">Next</a>
          </li>
        </ul>
      </nav>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      todos: [],
      newTitle: "",
    };
  },
  async created() {
    await this.getData();
  },
  methods: {
    getData() {
      axios
        .all([
          axios.get("https://jsonplaceholder.typicode.com/todos"),
          axios.get("https://jsonplaceholder.typicode.com/users"),
        ])
        .then(
          (axios.spread = (response) => {
            var todos = response[0].data;
            var users = response[1].data;

            for (const t in todos) {
              let todo = todos[t];

              if (todo.completed === false) {
                todo.completed = "In Progress";
              } else if (todo.completed === true) {
                todo.completed = "Done";
              }

              for (const u in users) {
                let user = users[u];

                if (user.id == todo.userId) {
                  todo.userId = user.name;
                }
              }

              this.todos.push(todo);
            }
          })
        );
    },

    deleteTodo(todoID) {
      axios
        .delete("https://jsonplaceholder.typicode.com/todos/" + todoID)
        .then(() => {
          for (let i in this.todos) {
            var todo = this.todos[i];
            console.log(todo.id);
            if (todo.id == todoID) {
              this.todos.splice(i, 1);
            }
          }
        });
    },

    editTodo(todoID) {
      // let text = this.newTitle;

      // this.confirmEdit(text);

      console.log(todoID);

      axios
        .patch("https://jsonplaceholder.typicode.com/todos/" + todoID)
        .then(() => {});
    },
    confirmEdit(text) {
      console.log(text);
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  color: #2c3e50;
  margin-top: 60px;
}
</style>
