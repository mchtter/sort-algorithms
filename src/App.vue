<template>
  <div id="app">
    <table class="table align-middle">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Title</th>
          <th scope="col">Assignee</th>
          <th scope="col">Status <a href="#" @click="sortStatus()">{{sortIcon}}</a>
          </th>
          <th scope="col">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(todo, index) in todos.slice(
            pagination.firstValue,
            pagination.lastValue
          )"
          :key="index"
        >
          <th scope="row">{{ todo.id }}</th>
          <td>{{ todo.title }}</td>
          <td>{{ todo.userId }}</td>
          <td>{{ todo.completedText }}</td>
          <td>
            <button
              @click="editTodo(todo.id, todo.title, todo.completed)"
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
        </tr>
      </tbody>
    </table>

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
                  <input
                    type="text"
                    id="newTitle"
                    class="form-control"
                    v-model="newTitle"
                  />
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
                    id="isCompleted"
                    v-model="newStatus"
                  />
                  <label class="form-check-label" for="isCompleted">
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
              @click="confirmEdit(currentID, newTitle, newStatus)"
              type="button"
              class="btn btn-success"
              data-mdb-dismiss="modal"
            >
              Save changes
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- Modal -->

    <div class="d-flex justify-content-center">
      <nav>
        <ul class="pagination">
          <li class="page-item">
            <a @click="navigatePage('prev')" class="page-link" href="#"
              >Previous</a
            >
          </li>

          <li
            v-for="(page, index) in pagination.pages"
            :key="index"
            :class="['page-item', { 'active' : index === pagination.currentPage }]"

          >
            <a @click="navigatePage(index)" class="page-link" href="#">{{
              page
            }}</a>
          </li>

          <li class="page-item">
            <a @click="navigatePage('next')" class="page-link" href="#">Next</a>
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
      newStatus: null,
      currentID: null,
      pagination: {
        firstValue: 0,
        lastValue: 10,
        totalPage: null,
        pages: [],
        currentPage: 0,
      },
      isSorted: 0,
      sortIcon: "▼"
    };
  },
  async created() {
    this.getData();
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
                todo.completedText = "In Progress";
              } else if (todo.completed === true) {
                todo.completedText = "Done";
              }

              for (const u in users) {
                let user = users[u];

                if (user.id == todo.userId) {
                  todo.userId = user.name;
                }
              }

              this.todos.push(todo);
            }
            this.calculatePagination();
          })
        );
    },

    deleteTodo(todoID) {
      axios
        .delete("https://jsonplaceholder.typicode.com/todos/" + todoID)
        .then(() => {
          for (let i in this.todos) {
            var todo = this.todos[i];

            if (todo.id == todoID) {
              this.todos.splice(i, 1);
            }
          }
        });
    },

    editTodo(todoID, title, status) {
      this.currentID = todoID;
      this.newTitle = title;
      this.newStatus = status;
    },

    confirmEdit(todoID, newTitle, newStatus) {
      axios
        .patch("https://jsonplaceholder.typicode.com/todos/" + todoID)
        .then(() => {
          for (var i in this.todos) {
            var todo = this.todos[i];
            if (todo.id == todoID) {
              todo.title = newTitle;

              if (newStatus === false) {
                todo.completedText = "In Progress";
              } else if (newStatus === true) {
                todo.completedText = "Done";
              }
            }
          }
        });
    },

    calculatePagination() {
      var totalPage = this.todos.length;
      this.totalPage = totalPage;
      var perPage = 10;
      var numberOfPages = totalPage / perPage;
      for (var i = 1; i <= numberOfPages; i++) {
        this.pagination.pages.push(i);
      }
    },

    navigatePage(value) {
      if (value == "next") {
        if (this.pagination.lastValue != this.totalPage) {
          this.pagination.firstValue += 10;
          this.pagination.lastValue += 10;
          this.pagination.currentPage += 1
        }
      } else if (value == "prev") {
        if (this.pagination.firstValue != 0) {
          this.pagination.firstValue -= 10;
          this.pagination.lastValue -= 10;
          this.pagination.currentPage -= 1
        }
      } else {
        this.pagination.firstValue = 10 * value
        this.pagination.lastValue = (10 * value) + 10
        this.pagination.currentPage = value
      }
    },

    sortStatus() {
      if (this.isSorted == 0) {
        this.todos.sort((a, b) => a.completed > b.completed ? 1 : -1) 
        this.isSorted = 1
        this.sortIcon = "▲"
      } else if ( this.isSorted == 1) {
        this.todos.sort((a, b) => a.completed > b.completed ? -1 : 1) 
        this.isSorted = 0
        this.sortIcon = "▼"

      }

    }
  },
  computed: {},
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
