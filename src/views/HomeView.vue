<template>
  <v-container fluid class="container">
    <v-row :class="rowClass" justify="center">
      <v-col align-self="center" cols="11" sm="5">
        <!-- header -->
        <div class="d-flex">
          <p class="text-h4 text-white font-weight-bold">T O D O</p>
          <v-spacer></v-spacer>
          <v-icon
            size="30"
            color="white"
            @click="toggleTheme"
            :icon="
              isDark ? 'mdi-white-balance-sunny' : 'mdi-moon-waning-crescent'
            "
          ></v-icon>
        </div>
        <!-- input -->
        <v-form @submit.prevent="createTodo">
          <v-text-field
            label="Create a new todo..."
            variant="solo"
            class="mt-8 text-white"
            clearable
            v-model="newToDo"
            hint="Hit enter when done..."
          />
        </v-form>
      </v-col>
    </v-row>
    <v-row class="row-2" justify="center">
      <v-col cols="11" sm="5">
        <!-- list -->
        <v-card class="mt-n10" elevation="5">
          <draggable :list="showingTodos">
            <div v-for="(todo, i) in showingTodos" :key="i">
              <v-hover>
                <template v-slot:default="{ isHovering, props }">
                  <v-list-item v-bind="props" link class="pa-4" draggable>
                    <!-- prepend icon -->
                    <template v-slot:prepend>
                      <v-icon
                        v-if="todo.done"
                        color="primary"
                        @click="mark(todo)"
                        >mdi-check-circle</v-icon
                      >
                      <v-icon
                        v-else
                        :color="isHovering ? 'primary' : 'grey lighten-4'"
                        @click="mark(todo)"
                        >mdi-checkbox-blank-circle-outline</v-icon
                      >
                    </template>
                    <!-- list item text -->
                    <v-list-item-title
                      :class="
                        todo.done
                          ? 'text-decoration-line-through text-disabled'
                          : ''
                      "
                    >
                      {{ todo.title }}
                    </v-list-item-title>
                    <!-- append icon -->
                    <template v-slot:append v-if="isHovering || isMobile">
                      <v-icon color="primary" @click="remove(todo)"
                        >mdi-close</v-icon
                      >
                    </template>
                  </v-list-item>
                </template>
              </v-hover>
              <v-divider />
            </div>
          </draggable>
          <div v-if="showingTodos.length < 1" class="pa-2">
            <p class="text-center">No todos to see 😀</p>
          </div>
          <!-- actions -->
          <v-card-actions>
            <p class="text-caption text-medium-emphasis">
              {{ undone.length }} Items left
            </p>
            <v-spacer></v-spacer>
            <!-- sort buttons in desktop -->
            <div class="d-flex justify-space-between" v-if="!isMobile">
              <v-badge :content="todos.length">
                <v-btn
                  variant="plain"
                  size="small"
                  class="text-capitalize font-weight-bold text-medium-emphasis"
                  :color="sort == '' ? 'primary' : ''"
                  @click="sort = ''"
                  >All</v-btn
                >
              </v-badge>
              <v-badge :content="undone.length">
                <v-btn
                  variant="plain"
                  size="small"
                  class="text-capitalize font-weight-bold text-medium-emphasis"
                  :color="sort == 'active' ? 'primary' : ''"
                  @click="sort = 'active'"
                  >Active</v-btn
                >
              </v-badge>
              <v-badge :content="done.length">
                <v-btn
                  variant="plain"
                  size="small"
                  class="text-capitalize font-weight-bold text-medium-emphasis"
                  :color="sort == 'undone' ? 'primary' : ''"
                  @click="sort = 'undone'"
                  >Completed</v-btn
                >
              </v-badge>
            </div>
            <v-spacer></v-spacer>
            <v-btn
              variant="plain"
              class="text-caption text-capitalize"
              @click="clearComplete"
              >clear completed</v-btn
            >
          </v-card-actions>
        </v-card>
        <!-- sort buttons in mobile -->
        <div class="d-flex justify-space-around mt-7" v-if="isMobile">
          <v-badge :content="todos.length">
            <v-btn
              variant="plain"
              size="small"
              class="text-capitalize font-weight-bold text-medium-emphasis"
              :color="sort == '' ? 'primary' : ''"
              @click="sort = ''"
              >All</v-btn
            >
          </v-badge>
          <v-badge :content="undone.length">
            <v-btn
              variant="plain"
              size="small"
              class="text-capitalize font-weight-bold text-medium-emphasis"
              :color="sort == 'active' ? 'primary' : ''"
              @click="sort = 'active'"
              >Active</v-btn
            >
          </v-badge>
          <v-badge :content="done.length">
            <v-btn
              variant="plain"
              size="small"
              class="text-capitalize font-weight-bold text-medium-emphasis"
              :color="sort == 'undone' ? 'primary' : ''"
              @click="sort = 'undone'"
              >Completed</v-btn
            >
          </v-badge>
          <draggable>heyyy</draggable>
        </div>
        <p class="text-center mt-4 text-medium-emphasis">
          Drag and drop to reorder list
        </p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { defineComponent } from "vue";
import { VueDraggableNext } from "vue-draggable-next";

export default defineComponent({
  name: "HomeView",
  data: () => ({
    todos: [
      {
        title: "Complete online JavaScript course",
        done: false,
      },
      {
        title: "Jog around the park 3x",
        done: false,
      },
      {
        title: "10 minutes meditation",
        done: false,
      },
      {
        title: "Read for 1 hour",
        done: false,
      },
      {
        title: "Pick up groceries",
        done: false,
      },
      {
        title: "Complete ToDo app on Frontend Mentor",
        done: false,
      },
    ],
    showingTodos: [],
    newToDo: "",
    sort: "",
  }),
  computed: {
    // incomplete todos
    undone() {
      return this.todos.filter((t) => !t.done);
    },
    // done todos
    done() {
      return this.todos.filter((t) => t.done);
    },
    // current theme
    isDark() {
      return this.$vuetify.theme.global.current.dark;
    },
    // current device
    isMobile() {
      return this.$vuetify.display.xs;
    },
    rowClass() {
      if (!this.isMobile && !this.isDark) return "row-1";
      else if (!this.isMobile && this.isDark) return "row-1-dark";
      else if (this.isMobile && !this.isDark) return "row-1-mobile";
      else return "row-1-mobile-dark";
    },
  },
  watch: {
    sort(newVal, oldVal) {
      if (newVal !== oldVal) this.getTodos();
    },
  },
  methods: {
    // save todo to local storage
    saveToLocalStorage() {
      localStorage.setItem("todos", JSON.stringify(this.todos));
    },

    // create a todo
    createTodo() {
      this.todos.push({
        title: this.newToDo,
        done: false,
      });
      this.saveToLocalStorage();
      this.newToDo = "";
      this.getTodos();
    },
    // mark a todo as done/undone
    mark(todo) {
      let ref = this.todos;
      this.todos[ref.indexOf(todo)].done = !this.todos[ref.indexOf(todo)].done;
      localStorage.clear();
      this.saveToLocalStorage();
      this.getTodos();
    },
    // remove a single done todo
    remove(todo) {
      localStorage.clear();
      this.todos.splice(this.todos.indexOf(todo), 1);
      this.saveToLocalStorage();
      this.getTodos();
    },
    // remove done todos
    clearComplete() {
      localStorage.clear();
      this.done.forEach((todo) => {
        this.todos.splice(this.todos.indexOf(todo), 1);
      });
      this.saveToLocalStorage();
      this.getTodos();
    },
    // get todos
    getTodos() {
      if (this.sort == "active") this.showingTodos = this.undone;
      else if (this.sort == "undone") this.showingTodos = this.done;
      else this.showingTodos = this.todos;
    },
    // toggle theme
    toggleTheme() {
      if (this.isDark) this.$vuetify.theme.global.name = "light";
      else this.$vuetify.theme.global.name = "dark";
    },
  },
  components: {
    draggable: VueDraggableNext,
  },
  mounted() {
    const storedTodos = localStorage.getItem("todos");
    this.todos = storedTodos ? JSON.parse(storedTodos) : this.todos;
    this.getTodos();
  },
});
</script>
<style scoped>
.container {
  margin: 0;
  padding: 0;
  height: 100vh;
}

.row-1 {
  height: 40vh;
  background-image: url("../assets/bg-desktop-light.jpg");
}
.row-1-dark {
  height: 40vh;
  background-image: url("../assets/bg-desktop-dark.jpg");
}
.row-1-mobile {
  height: 40vh;
  background-image: url("../assets/BG-MOBILE-LIGHT.JPG");
  background-size: cover;
}
.row-1-mobile-dark {
  height: 40vh;
  background-image: url("../assets/BG-MOBILE-DARK.JPG");
  background-size: cover;
}
</style>
